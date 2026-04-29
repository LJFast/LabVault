# LabVault — Architecture

This document captures the design of the LabVault system: what it is, how data flows through it, and the rules that aren't obvious from the code. Read this before making structural changes or processing new batches of source material.

For setup instructions see `README.md`. For per-analyte details see `v1/_meta/analyte_registry.md`. For category groupings see `v1/_meta/analyte_categories.md`.

## Mental model

LabVault is a **personal, local, privacy-first** longitudinal store for lab and imaging data. The user transcribes data from PDF reports (primarily USB Universitätsspital Basel, but provider-agnostic by design) into Obsidian markdown notes. Dashboards aggregate across notes. When AI-assisted clinical interpretation is wanted, an anonymised snapshot is generated from the vault and pasted into a fresh Claude conversation — the source PDFs themselves never leave the user's machine in routine operation.

The system is **not**:

- A clinical decision tool. Clinical interpretation requires the user's care team.
- A complete EHR. It tracks lab/imaging values and basic study findings; it does not capture medication lists, problem lists, or care plans.
- A real-time data source. Data lands in the vault when the user transcribes it, typically minutes to hours after a report is received.

The system **is**:

- A queryable longitudinal store of structured lab values, imaging measurements, and brief findings narratives
- A consistent format for AI-assisted interpretation snapshots
- A backup of the user's own clinical data in a portable, durable format (markdown + YAML)

## Two locations, two roles

There are two distinct filesystem locations. Confusing these breaks the privacy model.

**Live vault** at `~/Documents/Health/Jonas Lab Results/ObsidianLabVault/`
- Contains real lab data with actual values
- Opened daily in Obsidian
- **Never** in Git, never synced to cloud (no iCloud, Dropbox, OneDrive, etc.)
- Backed up by encrypted local backup only (Time Machine on FileVault'd disk)

**OUTPUT repo** at `~/Claude_CoWork/OUTPUT/LabVault/`, mirrored to `https://github.com/LJFast/LabVault` (private)
- Contains structural artifacts only: templates, schema, dashboards, registry, categories config, README, this architecture doc
- Tracked in Git, pushed to GitHub
- `.gitignore` defensively blocks `Labs/`, `Imaging/`, `Reports/` folders so accidental data leaks are caught
- Never contains a single real lab value

The structural skeleton lives under `v1/` in the repo. If a structural change is breaking (e.g., schema refactor that requires migrating existing notes), create `v2/` and write a migration note. Cosmetic changes go directly into `v1/`.

## Data flow

```
Source PDF (local, encrypted disk)
         │
         │  manual transcription
         ▼
Live vault note (YAML frontmatter + markdown body)
         │
         │  Dataview queries
         ▼
Dashboards (overview, trends, snapshot_for_claude)
         │
         │  user copies snapshot
         ▼
Fresh Claude conversation (anonymised, no PDFs uploaded)
```

PDFs are the source of truth for raw values. Vault notes are the source of truth for queryable data. Dashboards are derived; they can be regenerated from notes anytime.

When a discrepancy is found between a note and a PDF, the PDF wins — fix the note.

## Folder layout (live vault)

```
ObsidianLabVault/
├── 00_README.md                 onboarding doc visible inside Obsidian
├── _meta/
│   ├── analyte_registry.md     canonical names, units, aliases (per-analyte reference)
│   ├── analyte_categories.md   clinical groupings (drives dashboard order)
│   └── personal_targets.md     clinician-set targets specific to this user
├── _templates/
│   ├── lab_draw.md             Templater scaffold for new lab draws
│   ├── tte.md                  Templater scaffold for echocardiogram
│   ├── imaging_report.md       generic imaging
│   └── other_report.md         consults, biopsies, discharge summaries
├── _dashboards/
│   ├── overview.md             latest values per analyte, grouped, plus out-of-range
│   ├── trends.md               time-series charts
│   └── snapshot_for_claude.md  anonymised export for AI interpretation
├── Labs/YYYY/                  one note per draw_date; filename YYYY-MM-DD-lab.md
├── Imaging/YYYY/               one note per study; filename YYYY-MM-DD-modality-region.md
├── Reports/YYYY/               consults, biopsies, discharge summaries
└── Backfill/                   manifests from bulk-import batches (audit trail)
```

The OUTPUT repo holds the same structure under `v1/`, minus the `Labs/`, `Imaging/`, `Reports/`, `Backfill/` folders (which are blocked by `.gitignore`).

## Schema: the lab_draw note

Every lab draw note has YAML frontmatter and a markdown body. The frontmatter is what dashboards read; the body is for human commentary.

### Required frontmatter fields

| Field | Type | Notes |
|---|---|---|
| `type` | string | Always `lab_draw` for these notes |
| `draw_date` | date (YYYY-MM-DD) | Date the blood was drawn (Probenentnahme), not the report date |
| `lab_provider` | string | USB / Viollier / Unilabs / Synlab / Risch / Other |
| `results` | list of objects | One entry per analyte — see below |

### Optional frontmatter fields

| Field | Type | Notes |
|---|---|---|
| `report_date` | date | When the report was issued (often same day or day after draw_date) |
| `context` | string | `routine` / `acute` / `post_procedure` / `baseline` / `other` |
| `fasting` | bool | Whether sample was fasting |
| `notes` | string | One-line note for the entry overall |

### Schema for each entry in `results:`

| Field | Required | Notes |
|---|---|---|
| `analyte` | yes | Canonical name from `analyte_registry.md`. Lowercase snake_case. |
| `value` | yes | Numeric for quantitative; string for qualitative dipstick (e.g., `"negativ"`) |
| `unit` | recommended | Stored explicitly per result. Never assume. SI units preferred. |
| `range` | recommended | List `[lower, upper]`; use `null` for one-sided ranges |
| `formula` | for eGFR | e.g., `CKD-EPI-2021` — required for eGFR to track formula changes across labs |
| `method` | optional | e.g., `LC-MS` for tacrolimus — relevant when assay matters |
| `time_post_dose` | for drug levels | Hours since last dose (when known) |
| `assay_lab` | for PCR assays | The lab that ran it — viral PCRs are not standardised across providers |
| `detection_limit` | for PCR | The lab's reported detection threshold |
| `note` | optional | Free-text annotation specific to this result |

### Range encoding

USB reports use three formats: `5.0–7.0`, `<10.0`, `>1`. Encode them as:

| Lab printed | YAML | Dashboard renders |
|---|---|---|
| `5.0 – 7.0` | `range: [5.0, 7.0]` | `5.0–7.0` |
| `<10.0` | `range: [null, 10.0]` | `≤10.0` |
| `>1` | `range: [1, null]` | `≥1` |
| (no range printed) | `range:` (empty) or omit field | (no range column) |

Below-detection values like `<1 cells/µL` are stored as the threshold value with a `note` field: `value: 1` plus `note: "<1 on report (below detection)"`. The dashboard treats them as numeric for trends; if a future requirement is to plot them as null, this convention can change.

## Schema: the imaging note

Imaging notes use frontmatter for `type: imaging`, `study_date`, `modality`, `region`, `indication`. Findings narrative goes in the markdown body, transcribed verbatim from the report with all identifiers removed.

For studies with stable structured measurements (TTE specifically), the frontmatter additionally has a `measurements:` list of `{parameter, value, unit, note}` objects, mirroring the result-list pattern from lab draws. This lets longitudinal measurements (LVEF, LA volume index, aortic root dimensions, etc.) be trended across studies.

## Categories config

`v1/_meta/analyte_categories.md` defines the clinical grouping used by the overview and snapshot dashboards. It's the single source of truth for grouping; the registry is for per-analyte details.

The frontmatter has a `categories:` list. Each category has `id`, `label`, `analytes` (list of canonical names). Order of categories in the list = display order in the dashboard. Order of analytes within a category = display order within that section.

Any analyte appearing in a draw but not listed in any category is grouped under "Other (uncategorised)" automatically. This is intentional — adding a new analyte to a draw never causes it to silently disappear from the dashboard. Add it to the appropriate category when convenient.

Current categories (as of v1, after batch 2):

```
renal · acid_base · bone_mineral
heme_red · heme_platelets · heme_white
inflammation · liver (incl. pancreas)
iron · cardiac · lipids · metabolic · vitamins
transplant_drugs · transplant_viral
tumor_markers · thyroid
urine_dipstick · urine_flow · urine_protein
```

The clinical rationale for the order: renal and acid-base sit adjacent because in CKD they read together; bone-mineral as one block because Ca/PO₄/PTH/ALP/vitD are interpreted as a coupled system; iron studies separate from inflammation despite ferritin being an acute-phase reactant, because it's part of the anemia workup story. See `_meta/analyte_categories.md` body section "Design rationale" for further detail.

## Identifier handling — what does NOT belong in vault notes

USB and similar reports contain identifying data that **must not** be transcribed into vault notes:

- Patient name, surname, given names
- Date of birth, sex (when sex matters for reference ranges, store it in `personal_targets.md` once, not in every draw)
- AHV / insurance number / social security
- Hospital MRN / internal references / "Unsere Referenzen" / "Ihre Referenzen"
- Auftragsnummer (lab order numbers)
- Ordering ward / department identifiers like "Med.6.2"
- Names of ordering or signing physicians
- Address fields

The "Source PDFs" line in a note's body may reference the source filename, but not its contents beyond the analyte values themselves. PDFs stay outside the vault on the local encrypted disk.

This isolation is what makes the snapshot-for-Claude workflow viable. The snapshot generator reads only frontmatter values — it cannot leak identifiers because identifiers were never stored.

## Special handling: tacrolimus

Tacrolimus interpretation depends entirely on sampling timing. A trough (drawn pre-dose, typically 12 h after the last dose) is interpreted against a target range. A post-dose level is not interpretable against the same range.

Two analytes exist:

- `tacrolimus_trough` — true pre-dose level. Has a `range` (the personal target). Plotted in trend.
- `tacrolimus_post_dose` — drawn shortly after dose. NO range field (a target makes no sense here). Excluded from the trough trend chart. Stored as a separate analyte to prevent contamination of trough analysis.

When transcribing a tacrolimus value, check the draw time relative to dose. If the draw was at the morning routine time and the patient's dose schedule has the morning dose immediately after, that's a trough. If the draw was hours after a dose (e.g., late evening when the morning dose was hours earlier, or specifically post-dose checks for toxicity), that's `tacrolimus_post_dose`.

When in doubt, store as `tacrolimus_post_dose` with a `note` explaining the uncertainty. Better to under-report troughs than to plot a post-dose value as a trough and mislead trend interpretation.

## Special handling: cumulative reports (USB pattern)

USB Kumulativbefunde present multiple draw dates side-by-side as columns in one PDF. This is for clinical context (showing the latest 2–4 measurements together), not because the columns belong in the same blood draw.

Each column = one draw_date = one note in the vault. A single PDF can therefore generate up to 4 notes.

When the same draw_date appears in multiple PDFs (e.g., a Tuesday morning Blutbild + Chemielabor + Toxikologie all draw blood at 07:00 Tuesday), those PDFs contribute to **one merged note** for that date, with all analytes combined under one `results:` list. The "Source PDFs" line lists all contributing filenames.

When the same draw_date appears in two cumulative reports printed on different dates (e.g., the 28.04 column appears in both the April Kumulativbefund and a later May Kumulativbefund), the note for that date is created once and the second occurrence is skipped. The manifest is the deduplication record.

## Backfill batches

Bulk imports are organised as batches. Each batch produces:

- Per-draw markdown files placed under `Labs/YYYY/` (and `Imaging/YYYY/` for imaging)
- A `_manifest.csv` recording every (date, type, source_pdfs, analyte_count) processed
- A `README.md` explaining what's in the batch, what was skipped, and any new analytes/categories added
- An `apply_batch_N.sh` script that idempotently places files into the live vault and records the manifest in `Backfill/`

When processing a new batch, the first step is to check existing manifests in `Backfill/` to avoid duplicating draw_dates already processed. The deduplication is by `draw_date`; if a draw_date has already been processed and a new PDF adds analytes to that same date, the existing note is **updated** (rewritten with the merged superset of analytes), not duplicated.

After a batch is applied:

- Live vault has the new notes
- The `Backfill/` folder has the manifest and batch README for audit trail
- The OUTPUT repo gets only structural changes (registry/categories updates if new analytes were introduced) — never the data files

## Snapshot format (snapshot_for_claude.md)

The snapshot is the canonical format for handing data to AI for interpretation. The dashboard generates it as a markdown code block, organised by clinical category, with one section per category, one analyte per line, and a small list of `date: value` pairs under each analyte.

```
## Renal function & volume

**creatinine** (µmol/L) [ref: 59–104]
  - 2025-11-07: 211
  - 2025-12-12: 283
  ...

**egfr** (mL/min/1.73m²) [ref: ≥90] {CKD-EPI-2021}
  - 2025-11-07: 29
  - 2025-12-12: 21
  ...
```

This format is deliberate:

- Categories give the LLM clinical context (it can reason about renal function as a unit, not as scattered analyte rows)
- Per-analyte unit and range shown once, reducing token waste
- Time-series under each analyte makes trend reading immediate
- No identifiers anywhere

The snapshot includes a "Clinical context" section the user fills in before pasting (age, condition, medications, recent changes, specific question). Snapshots are pasted into **fresh** Claude conversations, not added to existing project chats — a fresh context avoids carrying biases from prior conversations into current interpretation.

## Adding a new analyte

When a lab report includes an analyte not yet in the registry:

1. Add a row to `v1/_meta/analyte_registry.md` under the appropriate section: canonical name, unit, aliases (incl. German if from USB), notes
2. Add the canonical name to the appropriate category's `analytes:` list in `v1/_meta/analyte_categories.md`
3. Save both files; if changes are in the live vault, also sync to OUTPUT repo. Refresh dashboards in Obsidian.

If the analyte fits no existing category and is likely to recur, create a new category. Prefer categories defined by clinical interpretation domain (e.g., "anemia workup," "viral surveillance"), not by report origin (e.g., not "from Akutanalytik").

## Adding a new report type

For a new report type that's structurally different from existing templates (e.g., bone marrow biopsy report, autoimmune panel with novel structure):

1. Decide whether it fits the lab_draw schema (numeric values per analyte) or needs a new template
2. If new template needed: add `v1/_templates/<new_type>.md` following the Templater conventions used by existing templates
3. Document the new note `type:` value in this architecture doc
4. Add any new analytes to registry + categories
5. Update the overview dashboard if the new type needs its own listing (most don't — they fit under existing patterns)

## Versioning policy

The repo uses simple folder-versioning: `v1/`, `v2/`, etc. The current version is what active vaults use; older versions are kept for reference and migration. Within a version, cosmetic changes (new analyte, new chart, fixed query) go directly into the version folder and are committed as ordinary changes.

A version bump is triggered by a **breaking schema change**: anything that requires re-writing existing notes to match the new schema. When bumping, write a `docs/migration_v<old>_to_v<new>.md` describing what changed and how to migrate.

To date there have been no version bumps.

## Privacy boundaries — recap as a checklist

Before any AI interaction beyond this vault:

- [ ] Snapshot generated by the dashboard (not a raw PDF upload)
- [ ] No name, DOB, MRN, AHV, insurance number visible
- [ ] No hospital/department identifier that uniquely identifies the user
- [ ] Clinical context section reviewed: only what's relevant to the question
- [ ] Conversation is fresh, not in a project containing other identifying data

Before any commit to the OUTPUT repo:

- [ ] `git status` shows no `Labs/`, `Imaging/`, `Reports/`, or `Backfill/` paths
- [ ] `git diff --stat HEAD` shows changes only in `v1/`, `docs/`, `README.md`, or `.gitignore`

Before backing up the live vault:

- [ ] Backup destination is local or encrypted external (no cloud sync without a deliberate decision)

## Sources and references

- KDIGO 2024 Clinical Practice Guideline for the Evaluation and Management of CKD: https://kdigo.org/guidelines/ckd-evaluation-and-management/
- KDIGO 2017 Clinical Practice Guideline Update for the Diagnosis, Evaluation, Prevention, and Treatment of CKD–MBD: https://kdigo.org/guidelines/ckd-mbd/
- ASE/EACVI 2015 Recommendations for Cardiac Chamber Quantification by Echocardiography (source for TTE reference values): https://www.asecho.org/guideline/recommendations-for-cardiac-chamber-quantification-by-echocardiography-in-adults/
- Obsidian Dataview API and JS reference: https://blacksmithgu.github.io/obsidian-dataview/api/code-reference/
- Obsidian Templater syntax: https://silentvoid13.github.io/Templater/syntax/
- IFCC unit conventions: https://www.ifcc.org/
- For Swiss assay-specific reference ranges, the lab's own report header is authoritative — assays are not standardised across labs.

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

# Personal Lab Value Tracker — Vault README

This vault is your local, private store for lab results, imaging narratives, and other clinical reports. Nothing here syncs to any cloud unless you configure it to. Source PDFs stay on your laptop.

## Core principle

**Notes are the source of truth for queryable data; PDFs are archival.** When you want help interpreting results, you generate a clean, de-identified snapshot from this vault and paste *that* into Claude — not the original PDF.

## Required plugins

Install via Settings → Community plugins:

1. **Dataview** (essential) — powers the dashboards and the Claude-snapshot generator.
2. **Templater** (essential) — drives the new-lab-draw template with date prompts.
3. **Obsidian Charts** (recommended) — trend plots per analyte. Search "Charts" by Johannes Theiner.
4. **Periodic Notes** (optional) — only if you want daily/weekly review notes; not required for tracking.

After installing Dataview: Settings → Dataview → enable "JavaScript Queries" and "Inline JavaScript Queries". The dashboards use DQL (Dataview's own query language), but enabling JS leaves the door open for more complex views later.

After installing Templater: Settings → Templater → set Template folder location to `_templates`.

## Folder layout

| Folder | Purpose |
|---|---|
| `_meta/` | Reference ranges, personal targets, analyte registry. Read-mostly. |
| `_templates/` | Templater templates. Don't touch unless updating the template. |
| `_dashboards/` | Live views built from Dataview queries. Read-only in practice. |
| `Labs/YYYY/` | One note per blood draw. Filename: `YYYY-MM-DD-lab.md`. |
| `Imaging/YYYY/` | One note per imaging study. Filename: `YYYY-MM-DD-modality-region.md`. |
| `Reports/YYYY/` | Consults, biopsy reports, discharge summaries, etc. |

## Daily workflow

**Adding a new lab draw:**
1. New file → run Templater command "Insert template" → choose `lab_draw`.
2. Templater prompts you for draw date and lab provider; fills the YAML.
3. Fill in the `results:` list under YAML — one entry per analyte that came back.
4. Save. The note is now picked up by all dashboards automatically.

**Reviewing trends:**
- Open `_dashboards/overview.md` for latest values and out-of-range flags.
- Open `_dashboards/trends.md` for time-series plots.

**Generating a snapshot for Claude:**
- Open `_dashboards/snapshot_for_claude.md`.
- Adjust the date range or analyte filter at the top of the file if needed.
- Copy the rendered table; paste into Claude with whatever clinical question you have.
- The snapshot contains values, units, ranges, and dates only — no name, no DOB, no MRN.

## Conventions

- **Dates: ISO 8601 (`YYYY-MM-DD`).** Always. No exceptions. Sortability depends on this.
- **Units: explicit per result.** Never assume. Switzerland uses SI (µmol/L for creatinine), but lab reports occasionally vary, and the `unit` field is what makes the data unambiguous.
- **Analyte names: from `_meta/analyte_registry.md`.** The registry has canonical names and aliases. Use the canonical form in YAML (`creatinine`, not `Crea` or `Krea`).
- **Reference ranges: store what's printed on the report.** The lab-reported range goes in each result entry. Population ranges live in `_meta/reference_ranges.md` as a fallback. Personal targets (e.g., your tacrolimus target) live in `_meta/personal_targets.md`.
- **eGFR formula: store it.** CKD-EPI 2009, CKD-EPI 2021 (race-free), and MDRD give different numbers. The `formula` field on the eGFR result prevents trend artefacts when labs switch formulas.

## What does NOT belong in this vault

- Identifiers you don't need: full name, DOB, insurance number, AHV/social security, MRN. The vault works fine with none of these. If you want a "this is me" note, keep it in a separate, encrypted file.
- Source PDFs. Keep them in a separate folder on your disk (encrypted if you can — macOS FileVault / Windows BitLocker / VeraCrypt). The vault references them by filename only if at all.

## Backup

Obsidian vaults are just folders of markdown files. Back them up like any other folder: Time Machine, an external drive, or an encrypted archive. Do not use Obsidian Sync, iCloud, or Dropbox for this vault unless you've thought through the implications — those services are convenient, but they put a copy of every result on a third-party server.

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

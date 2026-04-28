# Design Notes

Record of key design decisions and the reasoning behind them. Update when major decisions change.

## Architecture: Path A (Obsidian + Dataview, manual entry)

**Decided:** 2026-04-28.

**Considered alternatives:**

- **Path B** — Extend an existing clinical RAG system. Rejected: lab values are structured numeric time-series; vector retrieval is the wrong tool for this shape of data.
- **Path C** — Python pipeline that parses PDFs into Obsidian notes. Deferred. Manual entry from cumulative reports takes ~3–5 minutes per draw. Building a robust parser per lab provider is multi-day work, and providers vary in layout. Revisit if the volume justifies it.

**Implication:** All entry is manual. Templater minimises friction. The schema and templates were built so a future Path C extractor would emit the same notes — no work is wasted by starting with Path A.

## Schema: results as a YAML list of objects ("long" format)

**Decided:** 2026-04-28.

Each lab draw note has a `results:` list, where each entry is `{analyte, value, unit, range, ...}`. Wide format (one YAML key per analyte) was rejected because:

1. Per-result reference ranges are not expressible if all values are sibling keys.
2. Per-result metadata (formula for eGFR, time-post-dose for tacrolimus, assay lab for PCRs) needs object containers.
3. Dataview's `FLATTEN` operator was designed for this shape.

The cost is slightly more verbose entry, mitigated by Templater providing a comment-block template for new entries.

## Cumulative reports

**Decision:** One vault note per draw date, regardless of how many cumulative columns appear in the source PDF.

USB and other Swiss labs default to showing the prior 2–3 draw columns alongside the latest. Only the *latest* column is new data; earlier columns are already in the vault from previous entries (assuming continuous tracking).

## eGFR formula tracking

The `formula` field on each eGFR result is mandatory in practice. Reasons:

- CKD-EPI 2009, CKD-EPI 2021 (race-free), and MDRD give different eGFR values for the same creatinine.
- Switzerland has largely shifted to CKD-EPI 2021. Older records may use earlier formulas.
- Without the formula stored, trend plots can show artificial steps when a lab updates its calculation.

The trends dashboard includes a check that warns if mixed formulas are present in a single eGFR series.

## Privacy model

The vault holds values + units + ranges + dates. It does not hold:

- Patient name, DOB, sex (sex is implicit in personal targets where relevant)
- Hospital MRN, insurance number, AHV number
- Order numbers, ordering physician names, ward identifiers
- Source PDF binaries

The "snapshot for Claude" workflow generates an anonymised markdown table from the vault for AI-assisted interpretation. PDFs are never re-uploaded to AI services as part of routine review.

## TTE template

Echocardiography reports have a stable structure with a fixed set of measurements. The TTE template encodes these as YAML so longitudinal tracking of LVEF, LA volume, aortic root dimensions, etc. is queryable.

For other imaging (CT, MRI, US), the generic imaging template captures narrative findings. If a particular modality becomes a recurring surveillance study (e.g., aortic CT every 12 months with consistent measurements), that's a candidate for its own structured template.

## What's deferred

- **Pattern 3 interactive dropdown chart:** the trends dashboard supports configurable plots via single-line edits. A real dropdown would need custom HTML with event listeners — possible but more brittle. Build only if friction emerges.
- **PDF parser (Path C):** see "Architecture" above.
- **Multi-vault federation / project dashboard:** separate project. The CoWork OUTPUT directory of artifacts is its own deliverable.
- **Periodic Notes plugin:** not required for tracking. Add if a journaling habit emerges.

## Open questions

- **Reference range divergence across labs:** when you eventually have data from multiple Swiss labs (Viollier, Unilabs, Synlab, Risch, USB), the schema captures per-result ranges as printed — but trend interpretation needs human attention to range differences. The dashboard does not currently warn when a series spans labs with different reference ranges; this could be added.
- **Personal targets review cadence:** `personal_targets.md` has a `review_due` field. The dashboard does not currently surface stale targets. Could be added as a small Dataview query.

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

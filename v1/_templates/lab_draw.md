<%*
// Templater template: lab_draw
// Prompts for draw date and lab provider, then sets up the YAML frontmatter
// and a results scaffold. After insertion, fill in the results list manually.

const drawDate = await tp.system.prompt("Draw date (YYYY-MM-DD)", tp.date.now("YYYY-MM-DD"));
const labProvider = await tp.system.suggester(
  ["Viollier", "Unilabs", "Synlab", "Risch", "USB (Universitätsspital Basel)", "Other"],
  ["Viollier", "Unilabs", "Synlab", "Risch", "USB", "Other"]
);
const drawContext = await tp.system.suggester(
  ["routine outpatient", "acute / unwell", "post-procedure", "stable baseline", "other"],
  ["routine", "acute", "post_procedure", "baseline", "other"]
);

// Rename file to YYYY-MM-DD-lab.md if it's still untitled
const targetName = `${drawDate}-lab`;
if (tp.file.title.startsWith("Untitled")) {
  await tp.file.rename(targetName);
}
-%>
---
type: lab_draw
draw_date: <% drawDate %>
report_date:
lab_provider: <% labProvider %>
context: <% drawContext %>
fasting:
notes:
results:
  # Add result entries below. Copy the template block as needed.
  # Use canonical analyte names from _meta/analyte_registry.md.
  #
  # - analyte: creatinine
  #   value: 95
  #   unit: µmol/L
  #   range: [62, 106]
  #   flag:                    # leave empty; dashboard computes ↑/↓/N
  #
  # - analyte: egfr
  #   value: 78
  #   unit: mL/min/1.73m²
  #   range: [90, null]
  #   formula: CKD-EPI-2021
  #
  # - analyte: tacrolimus_trough
  #   value: 6.2
  #   unit: µg/L
  #   range: [5, 7]            # personal target, not lab range
  #   time_post_dose: 12       # hours since last dose
  #
  # - analyte: ebv_pcr
  #   value: 0
  #   unit: copies/mL
  #   range: [0, null]
  #   assay_lab: <% labProvider %>
  #   detection_limit: 100
---

# Lab Draw — <% drawDate %>

**Provider:** <% labProvider %>  
**Context:** <% drawContext %>

## Clinical context at time of draw

<!-- e.g., current medications, recent changes, symptoms, suspected issues -->

## Results commentary

<!-- Anything the lab or clinician noted; your own observations on the trend -->

## Source

<!-- Reference to PDF filename (kept locally outside this vault), report number, or portal link.
     Do NOT paste identifying data here. -->

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

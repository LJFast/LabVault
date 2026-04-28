---
type: dashboard
purpose: snapshot_for_claude
---

# Snapshot for Claude

A clean, identifier-free table of recent lab values, ready to copy and paste when you want AI-assisted interpretation. **No name, DOB, MRN, or lab account number is included.** Only values, units, ranges, and dates.

## Configure the snapshot

Edit the parameters at the top of the dataviewjs block below to control:

- `monthsBack` — how far back to include (default: 6)
- `analyteFilter` — array of analytes to include, or `null` for all
- `includeContext` — whether to append a context block

## Generated snapshot

```dataviewjs
// === Configuration ===
const monthsBack = 6;
const analyteFilter = null;       // e.g., ["creatinine", "egfr", "tacrolimus_trough"] or null for all
const includeContext = true;

// === Build the snapshot ===
const cutoff = new Date();
cutoff.setMonth(cutoff.getMonth() - monthsBack);

const pages = dv.pages('"Labs"')
  .where(p => p.type === "lab_draw" && p.results && p.draw_date)
  .where(p => {
    const d = new Date(String(p.draw_date));
    return d >= cutoff;
  })
  .sort(p => String(p.draw_date), "asc");

// Collect all results into rows
const rows = [];
for (const p of pages) {
  for (const r of p.results) {
    if (analyteFilter && !analyteFilter.includes(r.analyte)) continue;
    if (r.value == null) continue;
    const rangeStr = (r.range && (r.range[0] != null || r.range[1] != null))
      ? `${r.range[0] ?? ""}–${r.range[1] ?? ""}`
      : "";
    rows.push({
      date: String(p.draw_date).slice(0, 10),
      analyte: r.analyte,
      value: r.value,
      unit: r.unit ?? "",
      range: rangeStr,
      formula: r.formula ?? ""
    });
  }
}

// Build markdown
let md = "```\n";
md += `# Lab snapshot — last ${monthsBack} months (generated ${dv.date("today").toFormat("yyyy-MM-dd")})\n\n`;

if (rows.length === 0) {
  md += "No results in selected window.\n";
} else {
  md += "| Date | Analyte | Value | Unit | Reference range | Notes |\n";
  md += "|------|---------|-------|------|-----------------|-------|\n";
  for (const r of rows) {
    md += `| ${r.date} | ${r.analyte} | ${r.value} | ${r.unit} | ${r.range} | ${r.formula} |\n`;
  }
}

if (includeContext) {
  md += `\n---\n`;
  md += `## Clinical context (edit before pasting if needed)\n\n`;
  md += `- Age / sex: \n`;
  md += `- Underlying condition: \n`;
  md += `- Current relevant medications: \n`;
  md += `- Recent changes (last 4 weeks): \n`;
  md += `- Specific question for this snapshot: \n`;
}

md += "```\n";

dv.paragraph(md);
```

## How to use

1. The block above renders a fenced code block with the snapshot.
2. **Click into the code block, select all (Ctrl/Cmd-A), copy.**
3. Open Claude in a fresh chat (not in a project where prior context may bias interpretation).
4. Paste, then **edit the "Clinical context" section** to fill in your situation and your specific question.
5. Send.

## Privacy checklist before pasting

- [ ] No name anywhere in the snapshot
- [ ] No DOB
- [ ] No MRN, insurance number, or lab account number
- [ ] No clinic/hospital identifier that uniquely identifies you
- [ ] Clinical context section reviewed — only what's relevant to the question

## Why this format

The table is structured (analyte, value, unit, range, date) so any LLM can parse it without ambiguity. Including the unit on every row prevents the model from silently assuming the wrong convention (e.g., creatinine in mg/dL when yours is µmol/L). Including the reference range lets the model flag direction without needing external knowledge of assay-specific ranges. Asking for a specific question forces a focused answer instead of a generic "your labs look like this" recap.

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

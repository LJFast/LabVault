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
// =====================

// ── Load category config ──
const configPage = dv.page("_meta/analyte_categories");
if (!configPage || !configPage.categories) {
  dv.paragraph("⚠️ Could not load category config from _meta/analyte_categories.md.");
} else {

const analyteToCategory = new Map();
const categories = [];
configPage.categories.forEach((cat, catIdx) => {
  categories.push({ id: cat.id, label: cat.label, idx: catIdx, analytes: cat.analytes ?? [] });
  (cat.analytes ?? []).forEach((a, aIdx) => {
    analyteToCategory.set(a, { id: cat.id, label: cat.label, catIdx, aIdx });
  });
});
const OTHER = { id: "other", label: "Other (uncategorised)", catIdx: 9999, aIdx: 9999 };

// ── Collect results in window ──
const cutoff = new Date();
cutoff.setMonth(cutoff.getMonth() - monthsBack);

const pages = dv.pages('"Labs"')
  .where(p => p.type === "lab_draw" && p.results && p.draw_date)
  .where(p => new Date(String(p.draw_date)) >= cutoff);

// Map: analyte → array of {date, value, unit, range, formula}
const series = new Map();
for (const p of pages) {
  const dateStr = String(p.draw_date).slice(0, 10);
  for (const r of p.results) {
    if (analyteFilter && !analyteFilter.includes(r.analyte)) continue;
    if (r.value == null) continue;
    if (!series.has(r.analyte)) series.set(r.analyte, []);
    series.get(r.analyte).push({
      date: dateStr,
      value: r.value,
      unit: r.unit ?? "",
      range: r.range ?? null,
      formula: r.formula ?? ""
    });
  }
}

// Sort each series by date asc
for (const arr of series.values()) {
  arr.sort((a, b) => a.date.localeCompare(b.date));
}

// ── Group analytes into categories ──
const grouped = new Map();
for (const [analyte, points] of series.entries()) {
  const cat = analyteToCategory.get(analyte) ?? OTHER;
  if (!grouped.has(cat.id)) {
    grouped.set(cat.id, { label: cat.label, catIdx: cat.catIdx, analytes: [] });
  }
  grouped.get(cat.id).analytes.push({ analyte, points, aIdx: cat.aIdx ?? 9999 });
}
for (const grp of grouped.values()) {
  grp.analytes.sort((a, b) => a.aIdx - b.aIdx);
}
const sortedGroups = [...grouped.values()].sort((a, b) => a.catIdx - b.catIdx);

// ── Build markdown ──
let md = "```\n";
md += `# Lab snapshot — last ${monthsBack} months (generated ${dv.date("today").toFormat("yyyy-MM-dd")})\n\n`;

if (sortedGroups.length === 0) {
  md += "No results in selected window.\n";
} else {
  for (const grp of sortedGroups) {
    md += `## ${grp.label}\n\n`;
    for (const a of grp.analytes) {
      // Use the unit and range from the most recent point as the canonical for the analyte
      const last = a.points[a.points.length - 1];
      const unit = last.unit ? ` (${last.unit})` : "";
      let rangeStr = "";
      if (last.range && (last.range[0] != null || last.range[1] != null)) {
        const lo = last.range[0];
        const hi = last.range[1];
        if (lo == null)      rangeStr = ` [ref: ≤${hi}]`;
        else if (hi == null) rangeStr = ` [ref: ≥${lo}]`;
        else                 rangeStr = ` [ref: ${lo}–${hi}]`;
      }
      const formula = last.formula ? ` {${last.formula}}` : "";
      md += `**${a.analyte}**${unit}${rangeStr}${formula}\n`;
      for (const p of a.points) {
        md += `  - ${p.date}: ${p.value}\n`;
      }
      md += `\n`;
    }
  }
}

if (includeContext) {
  md += `---\n## Clinical context (edit before pasting if needed)\n\n`;
  md += `- Age / sex: \n`;
  md += `- Underlying condition: \n`;
  md += `- Current relevant medications: \n`;
  md += `- Recent changes (last 4 weeks): \n`;
  md += `- Specific question for this snapshot: \n`;
}

md += "```\n";

dv.paragraph(md);

}  // close else
```

> **Format note:** The snapshot now groups analytes by clinical category, with each analyte's time-series shown as a small list. This gives an LLM clear context for reasoning ("here's renal function over time, here's bone-mineral over time") rather than a flat table that mixes axes. Unit and reference range are shown once per analyte (taken from the most recent point), reducing redundancy. If you want the old flat table format back, that's a one-block change — let me know.

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

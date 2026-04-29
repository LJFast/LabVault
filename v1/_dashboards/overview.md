---
type: dashboard
purpose: overview
---

# Overview Dashboard

Latest values across all tracked analytes, organised by clinical category, plus flags for out-of-range results from the most recent draw. Updates automatically when you add new lab draw notes.

The "Latest value per analyte" section is grouped using `_meta/analyte_categories.md`. Edit that file to change the grouping or order.

## Latest value per analyte

Grouped by clinical category. Out-of-range values are flagged with ↑ or ↓; in-range values show "N". Categories and grouping are defined in `_meta/analyte_categories.md`.

```dataviewjs
// ── Load the category config from _meta/analyte_categories.md ──
const configPage = dv.page("_meta/analyte_categories");
if (!configPage || !configPage.categories) {
  dv.paragraph("⚠️ Could not load category config from _meta/analyte_categories.md. Check that the file exists and has a `categories` field in its frontmatter.");
} else {

// Build a map: analyte → {categoryId, label, orderInCategory}
const analyteToCategory = new Map();
const categoryOrder = [];
configPage.categories.forEach((cat, catIdx) => {
  categoryOrder.push({ id: cat.id, label: cat.label, idx: catIdx });
  (cat.analytes ?? []).forEach((a, aIdx) => {
    analyteToCategory.set(a, { id: cat.id, label: cat.label, catIdx, aIdx });
  });
});
// Catch-all for uncategorised analytes
const OTHER = { id: "other", label: "Other (uncategorised)", catIdx: 9999 };

// ── Build map: analyte → most recent result ──
const latest = new Map();
const pages = dv.pages('"Labs"').where(p => p.type === "lab_draw" && p.results && p.draw_date);

for (const p of pages) {
  const dateStr = String(p.draw_date).slice(0, 10);
  for (const r of p.results) {
    if (!r.analyte || r.value == null) continue;
    const existing = latest.get(r.analyte);
    if (!existing || dateStr > existing.date) {
      latest.set(r.analyte, {
        analyte: r.analyte,
        value: r.value,
        unit: r.unit ?? "",
        range: r.range ?? null,
        date: dateStr,
        formula: r.formula ?? ""
      });
    }
  }
}

// ── Group latest values by category ──
const byCategory = new Map();
for (const r of latest.values()) {
  const cat = analyteToCategory.get(r.analyte) ?? OTHER;
  if (!byCategory.has(cat.id)) {
    byCategory.set(cat.id, { label: cat.label, catIdx: cat.catIdx, rows: [] });
  }
  byCategory.get(cat.id).rows.push({
    ...r,
    aIdx: cat.aIdx ?? 9999
  });
}

// Sort each category's rows by aIdx (declared order in config)
for (const grp of byCategory.values()) {
  grp.rows.sort((a, b) => a.aIdx - b.aIdx);
}

// Sort categories by catIdx (declared order in config)
const sortedCategories = [...byCategory.values()].sort((a, b) => a.catIdx - b.catIdx);

// ── Helpers ──
const flagFor = (val, range) => {
  if (!range || (range[0] == null && range[1] == null)) return "";
  if (range[0] != null && Number(val) < Number(range[0])) return "↓ low";
  if (range[1] != null && Number(val) > Number(range[1])) return "↑ high";
  return "N";
};

const formatRange = (range) => {
  if (!range) return "";
  const lo = range[0];
  const hi = range[1];
  if (lo == null && hi == null) return "";
  if (lo == null) return `≤${hi}`;
  if (hi == null) return `≥${lo}`;
  return `${lo}–${hi}`;
};

// ── Render ──
if (sortedCategories.length === 0) {
  dv.paragraph("*No lab results yet. Add a draw note under Labs/.*");
} else {
  for (const grp of sortedCategories) {
    dv.header(3, grp.label);
    dv.table(
      ["Analyte", "Latest", "Unit", "Range", "Flag", "Date"],
      grp.rows.map(r => [
        r.analyte,
        r.value,
        r.unit,
        formatRange(r.range),
        flagFor(r.value, r.range),
        r.date
      ])
    );
  }
}

}  // close else
```

## Out-of-range from latest draw

Shows only results that are outside their reference range, from the most recent lab draw.

```dataviewjs
const pages = dv.pages('"Labs"').where(p => p.type === "lab_draw" && p.results && p.draw_date);

if (pages.length === 0) {
  dv.paragraph("*No lab draws yet.*");
} else {
  // Find the most recent draw
  const sorted = [...pages].sort((a, b) =>
    String(b.draw_date).localeCompare(String(a.draw_date))
  );
  const latest = sorted[0];
  const latestDate = String(latest.draw_date).slice(0, 10);

  dv.paragraph(`Latest draw: **${latestDate}** (${latest.lab_provider ?? "unknown lab"})`);

  // Filter to out-of-range only
  const outOfRange = [];
  for (const r of latest.results) {
    if (r.value == null || !r.range) continue;
    const v = Number(r.value);
    const low = r.range[0];
    const high = r.range[1];
    let flag = "";
    if (low != null && v < Number(low)) flag = "↓ low";
    else if (high != null && v > Number(high)) flag = "↑ high";
    if (flag) {
      const lo = r.range[0];
      const hi = r.range[1];
      let rangeStr;
      if (lo == null) rangeStr = `≤${hi}`;
      else if (hi == null) rangeStr = `≥${lo}`;
      else rangeStr = `${lo}–${hi}`;
      outOfRange.push({
        analyte: r.analyte,
        value: r.value,
        unit: r.unit ?? "",
        range: rangeStr,
        flag
      });
    }
  }

  if (outOfRange.length === 0) {
    dv.paragraph("✓ All results in range.");
  } else {
    dv.table(
      ["Analyte", "Value", "Unit", "Range", "Flag"],
      outOfRange.map(r => [r.analyte, r.value, r.unit, r.range, r.flag])
    );
  }
}
```

## Recent draws

```dataview
TABLE WITHOUT ID
  file.link as "Note",
  draw_date as "Date",
  lab_provider as "Lab",
  context as "Context",
  length(results) as "# results"
FROM "Labs"
WHERE type = "lab_draw"
SORT draw_date DESC
LIMIT 12
```

## Imaging studies

```dataview
TABLE WITHOUT ID
  file.link as "Study",
  study_date as "Date",
  modality as "Modality",
  region as "Region"
FROM "Imaging"
WHERE type = "imaging"
SORT study_date DESC
LIMIT 8
```

## Other reports

```dataview
TABLE WITHOUT ID
  file.link as "Report",
  report_date as "Date",
  specialty as "Specialty",
  report_type as "Type"
FROM "Reports"
WHERE type = "report"
SORT report_date DESC
LIMIT 8
```

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

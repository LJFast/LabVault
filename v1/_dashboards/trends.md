---
type: dashboard
purpose: trends
---

# Trends Dashboard

Time-series plots per analyte. Each chart uses the Obsidian Charts plugin and pulls data via `dataviewjs`. To add a new analyte, copy a block and change the `analyte` filter.

## How this works

The `dataviewjs` block extracts (date, value) pairs for one analyte across all your lab draw notes, then hands the data to the Charts plugin. If you don't see plots, check that:

1. Both **Dataview** and **Obsidian Charts** plugins are installed and enabled.
2. In Dataview settings, **JavaScript Queries** is turned on.
3. You have at least 2 draws containing the analyte (a single point doesn't render a line).

## Creatinine

```dataviewjs
const analyte = "creatinine";
const pages = dv.pages('"Labs"').where(p => p.type === "lab_draw" && p.results);
const points = [];
for (const p of pages) {
  for (const r of p.results) {
    if (r.analyte === analyte && r.value != null) {
      points.push({ date: p.draw_date, value: Number(r.value), unit: r.unit });
    }
  }
}
points.sort((a, b) => String(a.date).localeCompare(String(b.date)));

if (points.length === 0) {
  dv.paragraph(`*No ${analyte} results yet.*`);
} else {
  const chart = {
    type: "line",
    data: {
      labels: points.map(p => String(p.date).slice(0, 10)),
      datasets: [{
        label: `${analyte} (${points[0].unit})`,
        data: points.map(p => p.value),
        borderWidth: 2,
        tension: 0.2
      }]
    },
    options: { scales: { y: { beginAtZero: false } } }
  };
  window.renderChart(chart, this.container);
}
```

## eGFR

```dataviewjs
const analyte = "egfr";
const pages = dv.pages('"Labs"').where(p => p.type === "lab_draw" && p.results);
const points = [];
for (const p of pages) {
  for (const r of p.results) {
    if (r.analyte === analyte && r.value != null) {
      points.push({ date: p.draw_date, value: Number(r.value), unit: r.unit, formula: r.formula });
    }
  }
}
points.sort((a, b) => String(a.date).localeCompare(String(b.date)));

if (points.length === 0) {
  dv.paragraph(`*No ${analyte} results yet.*`);
} else {
  // If formulas mix, warn — values may not be comparable
  const formulas = [...new Set(points.map(p => p.formula).filter(Boolean))];
  if (formulas.length > 1) {
    dv.paragraph(`⚠️ Mixed eGFR formulas in dataset: ${formulas.join(", ")}. Trend may show artificial steps at formula transitions.`);
  }
  const chart = {
    type: "line",
    data: {
      labels: points.map(p => String(p.date).slice(0, 10)),
      datasets: [{
        label: `eGFR (${points[0].unit})`,
        data: points.map(p => p.value),
        borderWidth: 2,
        tension: 0.2
      }]
    },
    options: { scales: { y: { beginAtZero: false } } }
  };
  window.renderChart(chart, this.container);
}
```

## Tacrolimus trough

```dataviewjs
const analyte = "tacrolimus_trough";
const pages = dv.pages('"Labs"').where(p => p.type === "lab_draw" && p.results);
const points = [];
for (const p of pages) {
  for (const r of p.results) {
    if (r.analyte === analyte && r.value != null) {
      points.push({ date: p.draw_date, value: Number(r.value), unit: r.unit });
    }
  }
}
points.sort((a, b) => String(a.date).localeCompare(String(b.date)));

if (points.length === 0) {
  dv.paragraph(`*No ${analyte} results yet.*`);
} else {
  const chart = {
    type: "line",
    data: {
      labels: points.map(p => String(p.date).slice(0, 10)),
      datasets: [{
        label: `Tacrolimus C0 (${points[0].unit})`,
        data: points.map(p => p.value),
        borderWidth: 2,
        tension: 0.2
      }]
    },
    options: { scales: { y: { beginAtZero: false } } }
  };
  window.renderChart(chart, this.container);
}
```

## CRP

```dataviewjs
const analyte = "crp";
const pages = dv.pages('"Labs"').where(p => p.type === "lab_draw" && p.results);
const points = [];
for (const p of pages) {
  for (const r of p.results) {
    if (r.analyte === analyte && r.value != null) {
      points.push({ date: p.draw_date, value: Number(r.value), unit: r.unit });
    }
  }
}
points.sort((a, b) => String(a.date).localeCompare(String(b.date)));

if (points.length === 0) {
  dv.paragraph(`*No ${analyte} results yet.*`);
} else {
  const chart = {
    type: "line",
    data: {
      labels: points.map(p => String(p.date).slice(0, 10)),
      datasets: [{
        label: `CRP (${points[0].unit})`,
        data: points.map(p => p.value),
        borderWidth: 2,
        tension: 0.2
      }]
    },
    options: { scales: { y: { beginAtZero: false } } }
  };
  window.renderChart(chart, this.container);
}
```

## Hemoglobin

```dataviewjs
const analyte = "hemoglobin";
const pages = dv.pages('"Labs"').where(p => p.type === "lab_draw" && p.results);
const points = [];
for (const p of pages) {
  for (const r of p.results) {
    if (r.analyte === analyte && r.value != null) {
      points.push({ date: p.draw_date, value: Number(r.value), unit: r.unit });
    }
  }
}
points.sort((a, b) => String(a.date).localeCompare(String(b.date)));

if (points.length === 0) {
  dv.paragraph(`*No ${analyte} results yet.*`);
} else {
  const chart = {
    type: "line",
    data: {
      labels: points.map(p => String(p.date).slice(0, 10)),
      datasets: [{
        label: `Hemoglobin (${points[0].unit})`,
        data: points.map(p => p.value),
        borderWidth: 2,
        tension: 0.2
      }]
    },
    options: { scales: { y: { beginAtZero: false } } }
  };
  window.renderChart(chart, this.container);
}
```

## Custom plot — single analyte (configurable)

Edit the `analyte` and `monthsBack` lines below to plot anything from your registry without copy-pasting a new block. Change the value, then re-render the page (Ctrl/Cmd-E to toggle edit/preview, or use the command palette: "Dataview: Force refresh all views").

```dataviewjs
// === Edit these two lines ===
const analyte = "phosphate";        // any canonical name from _meta/analyte_registry.md
const monthsBack = 24;              // how far back to plot
// ============================

const cutoff = new Date();
cutoff.setMonth(cutoff.getMonth() - monthsBack);

const pages = dv.pages('"Labs"').where(p => p.type === "lab_draw" && p.results && p.draw_date);
const points = [];
for (const p of pages) {
  const d = new Date(String(p.draw_date));
  if (d < cutoff) continue;
  for (const r of p.results) {
    if (r.analyte === analyte && r.value != null) {
      points.push({ date: String(p.draw_date).slice(0, 10), value: Number(r.value), unit: r.unit, range: r.range });
    }
  }
}
points.sort((a, b) => a.date.localeCompare(b.date));

if (points.length === 0) {
  dv.paragraph(`*No ${analyte} results in the last ${monthsBack} months.*`);
} else {
  // If a range is present on the most recent point, draw it as horizontal reference lines
  const latestRange = points[points.length - 1].range;
  const datasets = [{
    label: `${analyte} (${points[0].unit})`,
    data: points.map(p => p.value),
    borderWidth: 2,
    tension: 0.2
  }];
  if (latestRange && latestRange[0] != null) {
    datasets.push({
      label: "Lower ref",
      data: points.map(() => latestRange[0]),
      borderDash: [5, 5],
      borderWidth: 1,
      pointRadius: 0
    });
  }
  if (latestRange && latestRange[1] != null) {
    datasets.push({
      label: "Upper ref",
      data: points.map(() => latestRange[1]),
      borderDash: [5, 5],
      borderWidth: 1,
      pointRadius: 0
    });
  }
  const chart = {
    type: "line",
    data: { labels: points.map(p => p.date), datasets },
    options: { scales: { y: { beginAtZero: false } } }
  };
  window.renderChart(chart, this.container);
}
```

## Custom plot — multi-analyte overlay

For plotting two or three related analytes together (e.g., calcium and phosphate, or eGFR and creatinine on dual axes). Edit the `analytes` list — each becomes a line on the same chart. **Note:** mixing units on one Y-axis is usually misleading. Use this for analytes that share a unit, or for visual co-trend without precise Y-axis interpretation.

```dataviewjs
// === Edit these lines ===
const analytes = ["calcium_total", "phosphate"];
const monthsBack = 24;
// ========================

const cutoff = new Date();
cutoff.setMonth(cutoff.getMonth() - monthsBack);

// Collect a sorted list of all unique dates across selected analytes
const series = {};
const allDates = new Set();
for (const a of analytes) series[a] = {};

const pages = dv.pages('"Labs"').where(p => p.type === "lab_draw" && p.results && p.draw_date);
for (const p of pages) {
  const d = new Date(String(p.draw_date));
  if (d < cutoff) continue;
  const dateKey = String(p.draw_date).slice(0, 10);
  for (const r of p.results) {
    if (analytes.includes(r.analyte) && r.value != null) {
      series[r.analyte][dateKey] = { value: Number(r.value), unit: r.unit };
      allDates.add(dateKey);
    }
  }
}

const sortedDates = [...allDates].sort();
if (sortedDates.length === 0) {
  dv.paragraph(`*No data for ${analytes.join(", ")} in selected window.*`);
} else {
  const datasets = analytes.map(a => {
    const firstUnit = Object.values(series[a])[0]?.unit ?? "";
    return {
      label: `${a} (${firstUnit})`,
      data: sortedDates.map(d => series[a][d]?.value ?? null),
      spanGaps: true,
      borderWidth: 2,
      tension: 0.2
    };
  });
  const chart = {
    type: "line",
    data: { labels: sortedDates, datasets },
    options: { scales: { y: { beginAtZero: false } } }
  };
  window.renderChart(chart, this.container);
}
```

## Adding a permanent new chart

Copy any single-analyte block from earlier in this file, change the `analyte` constant on the first line to the canonical name from `_meta/analyte_registry.md`, and update the dataset label.

If you want a fully interactive dropdown chart (Pattern 3 — pick analyte from a UI element), that's a separate build. Ask when you're ready and I'll add it.

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

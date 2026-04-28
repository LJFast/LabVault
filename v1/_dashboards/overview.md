---
type: dashboard
purpose: overview
---

# Overview Dashboard

Latest values across all tracked analytes, plus out-of-range flags. Updates automatically when you add new lab draw notes.

## Latest value per analyte

```dataview
TABLE WITHOUT ID
  rows.analyte as "Analyte",
  rows[0].value as "Latest",
  rows[0].unit as "Unit",
  rows[0].range as "Range",
  dateformat(rows[0].date, "yyyy-MM-dd") as "Date"
FROM "Labs"
WHERE type = "lab_draw"
FLATTEN results as r
FLATTEN object(
  "analyte", r.analyte,
  "value", r.value,
  "unit", r.unit,
  "range", r.range,
  "date", draw_date
) as result_row
GROUP BY result_row.analyte
SORT rows[0].date DESC
```

> **Note on the query above:** Dataview's `GROUP BY` doesn't natively give you "latest per analyte" — the query above sorts within groups so `rows[0]` is the most recent. If you have many draws and the dashboard feels slow, narrow the source to `"Labs/2026"` (current year) for quicker rendering.

## Out-of-range from latest draw

This pulls all results from the most recent lab draw and flags those outside the per-result range.

```dataview
TABLE WITHOUT ID
  r.analyte as "Analyte",
  r.value as "Value",
  r.unit as "Unit",
  r.range as "Range",
  choice(
    r.range[0] != null AND r.value < r.range[0], "↓ low",
    choice(
      r.range[1] != null AND r.value > r.range[1], "↑ high",
      "in range"
    )
  ) as "Flag"
FROM "Labs"
WHERE type = "lab_draw"
FLATTEN results as r
SORT draw_date DESC
LIMIT 30
```

> Adjust `LIMIT 30` upward if your most recent draw has more than 30 analytes. Dataview will list all results from recent draws — the most useful interpretation is to look at the top block (newest date).

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

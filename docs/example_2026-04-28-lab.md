---
type: lab_draw
draw_date: 2026-04-28
report_date: 2026-04-28
lab_provider: USB
context: routine
fasting:
notes: "Real-shape example built from an actual USB Kumulativbefund (28.04.2026 column). Identifiers removed. Use this as the format reference; replace or delete once you have your own entries flowing."
results:
  # ─── Hematology (from Blutbild Kumulativbefund) ───
  - analyte: hemoglobin
    value: 110
    unit: g/L
    range: [140, 180]
  - analyte: hematocrit
    value: 0.34
    unit: L/L
    range: [0.38, 0.52]
  - analyte: erythrocytes
    value: 3.41
    unit: T/L
    range: [4.50, 6.30]
  - analyte: mcv
    value: 99
    unit: fL
    range: [79, 95]
  - analyte: mch
    value: 32.3
    unit: pg
    range: [27.0, 33.2]
  - analyte: mchc
    value: 326
    unit: g/L
    range: [320, 360]
  - analyte: rdw
    value: 14.4
    unit: "%"
    range: [11.5, 14.5]
  - analyte: platelets
    value: 200
    unit: G/L
    range: [150, 450]
  - analyte: wbc
    value: 7.36
    unit: G/L
    range: [3.5, 10.0]

  # ─── Chemistry (from Chemielabor Kumulativbefund) ───
  - analyte: sodium
    value: 140
    unit: mmol/L
    range: [136, 145]
  - analyte: potassium
    value: 4.3
    unit: mmol/L
    range: [3.4, 4.5]
  - analyte: creatinine
    value: 295
    unit: µmol/L
    range: [59, 104]
  - analyte: egfr
    value: 21
    unit: mL/min/1.73m²
    range: [90, null]
    formula: CKD-EPI-2021
  - analyte: urea
    value: 21.5
    unit: mmol/L
    range: [3.4, 8.7]
  - analyte: crp
    value: 15.9
    unit: mg/L
    range: [null, 10.0]

  # ─── Tacrolimus trough (from Toxikologie Kumulativbefund) ───
  - analyte: tacrolimus_trough
    value: 8.8
    unit: µg/L
    range: [5, 7]                  # personal target — adjust per your team's current target
    method: LC-MS
    time_post_dose:                # fill in if known (typically 12h)
---

# Lab Draw — 2026-04-28 (USB)

**Provider:** USB (Universitätsspital Basel)
**Context:** routine
**Source PDFs:** `01BlutbildKumulativbefund_15582341221.pdf`, `03Chemielabor_15582350141.pdf`, `09Toxikologie_15582725543.pdf` *(filenames only, PDFs kept outside this vault)*

## Clinical context at time of draw

This is a real-shape example transcribed from an actual draw. In your own entries this section would briefly note:

- Current immunosuppression regimen and any recent dose changes
- Reason for the draw (e.g., "post-discharge follow-up", "tacrolimus trough monitoring", "evaluation of rising creatinine")
- Any acute symptoms or concerns
- Recent events that might explain abnormal values (e.g., recent infection for elevated CRP, dehydration for raised urea)

## Results commentary

In this draw:

- **Renal function:** Creatinine 295 µmol/L, eGFR 21 mL/min/1.73m² (CKD G4). Compared with the three preceding columns in the cumulative report, creatinine has trended *down* from 372 → 373 → 328 → 295 over four days, with eGFR recovering from 16 → 21. Suggests recovery from an acute insult on a chronic baseline.
- **Inflammation:** CRP 15.9 mg/L, also trending down from 178.4 → 45.4 → 27.6 → 15.9 over the same period.
- **Hematology:** Hb 110 g/L (low), normocytic-macrocytic. Stable around this level over the four-day series.
- **Tacrolimus:** 8.8 µg/L on 28.04, having been 10.6 on 22.04 and 4.4 on 21.04 — significant variability worth noting in clinical correlation.

(This commentary is illustrative of the kind of brief observation worth capturing per draw. It is not clinical interpretation — that's what the snapshot-for-Claude workflow is for, and what your nephrology team is for.)

## Source

Transcribed from USB Kumulativbefunde dated 28.04.2026. Source PDFs retained locally, outside this vault. No patient identifiers, order numbers, or staff names entered into this note.

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

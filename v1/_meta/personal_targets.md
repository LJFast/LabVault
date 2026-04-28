---
type: meta
purpose: personal_targets
last_reviewed: 2026-04-28
---

# Personal Targets

Clinician-set targets specific to you. These override population reference ranges in the dashboard's "in-target" check. Update this file when your clinician changes a target.

Each entry records *who* set the target and *when*, so you can trace it back. If the date is stale (e.g., over a year old without review), the dashboard will flag it.

## Active targets

```yaml
targets:
  - analyte: tacrolimus_trough
    lower: 5.0
    upper: 7.0
    unit: µg/L
    set_by: "Dr. [name], [clinic]"
    set_date: 2026-04-28
    rationale: "Maintenance phase, stable graft function"
    review_due: 2026-10-28

  # Example template — replace with your own targets as set by your clinician.
  # Remove the "example_*" entries before going live.
  - analyte: example_hemoglobin_target
    lower: 110
    upper: 150
    unit: g/L
    set_by: "Example only — not a real target"
    set_date: 2026-04-28
    rationale: "This is a placeholder. Delete once you've added real targets."
    review_due: 2026-10-28
```

## Notes on target-setting

- For **tacrolimus**, targets typically narrow over time post-transplant (e.g., 8–12 µg/L early, narrowing to 5–7 µg/L in maintenance). Confirm current target with your nephrology team.
- For **hemoglobin in CKD**, the KDIGO anemia guideline targets 100–115 g/L when on ESA therapy, but individual targets vary. Don't auto-apply without clinician input.
- For **phosphate in CKD G4–5**, KDIGO recommends lowering toward the normal range; specific upper bound is individualized.
- **EBV PCR** doesn't have a "target" per se — the threshold for action depends on the lab's assay and your clinician's threshold for intervention. Record the threshold here if your team has set one.

## Sources

- KDIGO Anemia in CKD guideline: https://kdigo.org/guidelines/anemia-in-ckd/
- KDIGO CKD-MBD guideline: https://kdigo.org/guidelines/ckd-mbd/
- Tacrolimus targets: see your transplant center's protocol; targets are center- and indication-specific.

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

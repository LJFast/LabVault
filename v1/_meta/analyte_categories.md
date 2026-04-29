---
type: meta
purpose: analyte_categories
categories:
  - id: renal
    label: "Renal function & volume"
    analytes: [creatinine, egfr, cystatin_c, urea, uric_acid, sodium, potassium, chloride]

  - id: acid_base
    label: "Acid-base & blood gas"
    analytes:
      - bicarbonate
      - ph
      - pco2
      - po2
      - base_excess
      - ph_venous
      - bicarbonate_actual_venous
      - base_excess_venous
      - pco2_venous
      - po2_venous
      - anion_gap_venous
      - lactate_whole_blood_venous
      - glucose_whole_blood_venous
      - sodium_whole_blood_venous
      - potassium_whole_blood_venous
      - chloride_whole_blood_venous

  - id: bone_mineral
    label: "Bone-mineral axis"
    analytes: [calcium_total, calcium_albumin_corrected, calcium_ionized, phosphate, magnesium, pth, vitamin_d_25oh, alkaline_phosphatase]

  - id: heme_red
    label: "Hematology — red cells"
    analytes: [hemoglobin, hematocrit, erythrocytes, mcv, mch, mchc, rdw, hypochromic_ec]

  - id: heme_platelets
    label: "Hematology — platelets"
    analytes: [platelets, mpv, pdw]

  - id: heme_white
    label: "Hematology — white cells & differential"
    analytes:
      - wbc
      - neutrophils_abs
      - neutrophils_pct
      - lymphocytes_abs
      - lymphocytes_pct
      - monocytes_abs
      - monocytes_pct
      - eosinophils_abs
      - eosinophils_pct
      - basophils_abs
      - basophils_pct

  - id: inflammation
    label: "Inflammation & immunology"
    analytes: [crp, procalcitonin, albumin, total_protein]

  - id: liver
    label: "Liver & pancreas"
    analytes: [alt, ast, ggt, bilirubin_total, ldh, amylase, pancreas_amylase]

  - id: iron
    label: "Iron studies"
    analytes: [ferritin, transferrin_saturation]

  - id: cardiac
    label: "Cardiac & muscle"
    analytes: [creatine_kinase, troponin_t_hs, troponin_i_hs, nt_probnp]

  - id: lipids
    label: "Lipids"
    analytes: [cholesterol_total, cholesterol_hdl, cholesterol_ldl, cholesterol_non_hdl, cholesterol_hdl_ratio, triglycerides]

  - id: metabolic
    label: "Metabolic"
    analytes: [glucose, hba1c, hba1c_mmol]

  - id: vitamins
    label: "Vitamins"
    analytes: [vitamin_b12, holotranscobalamin, folate_serum, folate_erythrocyte]

  - id: transplant_drugs
    label: "Transplant — drug levels"
    analytes: [tacrolimus_trough, tacrolimus_post_dose, ciclosporin_trough, everolimus_trough, mycophenolate_auc]

  - id: transplant_viral
    label: "Transplant — viral PCRs"
    analytes: [ebv_pcr, cmv_pcr, bk_pcr_blood, bk_pcr_urine, jcpyv_dna_urine, respiratory_panel_pcr]

  - id: tumor_markers
    label: "Tumor markers"
    analytes: [psa]

  - id: thyroid
    label: "Thyroid"
    analytes: [tsh, ft4, ft3]

  - id: urine_dipstick
    label: "Urine — dipstick"
    analytes:
      - urine_ph
      - urine_specific_gravity
      - urine_protein_dipstick
      - urine_glucose_dipstick
      - urine_ketones_dipstick
      - urine_bilirubin_dipstick
      - urine_nitrite_dipstick
      - urine_leukocyte_esterase
      - urine_hemoglobin_dipstick

  - id: urine_flow
    label: "Urine — flowcytometry"
    analytes: [urine_leukocytes_count, urine_erythrocytes_count, urine_bacteria_count, urine_squamous_epithelium]

  - id: urine_protein
    label: "Urine — quantitative protein"
    analytes:
      - urine_creatinine
      - urine_protein_total
      - urine_albumin
      - urine_alpha1_microglobulin
      - urine_protein_creatinine_ratio
      - urine_albumin_creatinine_ratio
      - urine_alpha1_microglobulin_creatinine_ratio
      - urine_protein_24h
---

# Analyte Categories

This file is the **single source of truth** for clinical grouping in the dashboards. The categories live in the YAML frontmatter above; both `_dashboards/overview.md` and `_dashboards/snapshot_for_claude.md` read from this file.

## How grouping works

The frontmatter defines a list of categories. Each category has:

- `id` — a short machine-readable identifier
- `label` — the heading shown in the dashboard
- `analytes` — the canonical analyte names that belong to this category

Categories appear in the order listed. Within each category, analytes appear in the order listed.

Any analyte that appears in a lab draw but is not listed under any category gets grouped under **"Other"** automatically. This way, adding a new analyte to a draw note never causes it to silently disappear from the dashboard.

## To change the grouping

1. Edit the `categories:` list in the frontmatter above.
2. Save the file.
3. In Obsidian, run `Dataview: Force refresh all views` (Cmd+P), or close and reopen the dashboard.

Changes apply to both the overview and snapshot dashboards immediately.

## Design rationale

Grouping is **clinical**, not alphabetical or strictly USB report-order:

- **Renal and acid-base are adjacent** because in CKD they read together — declining HCO₃⁻ alongside rising creatinine signals progressive renal acidosis.
- **Bone-mineral axis is one block** because Ca, PO₄, PTH, ALP, and 25-OH-vit-D are interpreted as a coupled system, not as independent values.
- **Hematology is split into red / platelets / white-and-differential** because these axes have different clinical interpretations (anemia workup vs. thrombocytopenia/thrombocytosis vs. infection/immunosuppression).
- **Iron studies are separate from inflammation** despite ferritin being an acute-phase reactant, because it's part of the anemia workup story.

If your clinical context shifts (e.g., focus moves from renal to hematology), you may want to re-order categories. The dashboard reads the order from this file, so reordering takes ~30 seconds.

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

---
type: meta
purpose: analyte_registry
---

# Analyte Registry

Canonical names, units, and aliases. Use the canonical name in YAML frontmatter of lab draw notes. The aliases column is for your reference when transcribing from reports written in German, French, or with non-standard abbreviations.

> **Clinical grouping** for dashboards is defined separately in `analyte_categories.md`. The headings below are organisational only — they do not control dashboard ordering.

## Renal & electrolytes

| Canonical | Unit (SI) | Aliases | Notes |
|---|---|---|---|
| `creatinine` | µmol/L | Crea, Kreatinin, Créatinine | Multiply by 0.0113 to get mg/dL |
| `egfr` | mL/min/1.73m² | GFR estimated, eGFR-CKD-EPI | Always store `formula` field |
| `urea` | mmol/L | BUN (US, mg/dL), Harnstoff | BUN mg/dL = urea mmol/L × 2.8 |
| `uric_acid` | µmol/L | Harnsäure | Treatment target for gout: <360, severe gout: <300 |
| `cystatin_c` | mg/L | Cys-C | Alternative GFR marker |
| `sodium` | mmol/L | Na, Natrium | |
| `potassium` | mmol/L | K, Kalium | Hemolysis falsely raises K |
| `chloride` | mmol/L | Cl, Chlorid | |
| `bicarbonate` | mmol/L | HCO3, Bikarbonat | Often labeled "Standardbicarbonat" on BGA |
| `calcium_total` | mmol/L | Ca, Kalzium | Albumin-correction may be needed |
| `calcium_albumin_corrected` | mmol/L | Ca corr, Calcium Albumin korrigiert | Corrected to alb=40 g/L |
| `calcium_ionized` | mmol/L | Ca²⁺, ionized Ca | More accurate in low-albumin states |
| `phosphate` | mmol/L | PO4, Phosphat, P | |
| `magnesium` | mmol/L | Mg, Magnesium | |

## Acid-base & blood gas

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `ph` | (unitless) | pH | Generic; venous unless specified |
| `ph_venous` | (unitless) | pH venös | Venous BGA; USB ref 7.320–7.430 |
| `pco2` | kPa | pCO2 | Generic |
| `pco2_venous` | kPa | pCO2 venös | Venous BGA; USB ref 4.90–6.70 kPa |
| `po2` | kPa | pO2 | Arterial unless specified |
| `po2_venous` | kPa | pO2 venös | Venous; USB ref 4.40–5.50 kPa. NOT comparable to arterial pO2 |
| `bicarbonate` | mmol/L | HCO3, Bikarbonat, Standardbicarbonat | Serum/plasma. SI =SI conventions |
| `bicarbonate_actual_venous` | mmol/L | Aktuelles Bicarbonat venös | Venous BGA; USB ref 21.0–28.0 mmol/L |
| `base_excess` | mmol/L | BE, ABE | Generic |
| `base_excess_venous` | mmol/L | Base Excess venös | Venous BGA; USB ref −2 to +3 mmol/L |
| `anion_gap_venous` | mmol/L | Anionenlücke venös | Calculated; USB ref 8.0–16.0 |
| `lactate_whole_blood_venous` | mmol/L | Lactat Vollblut venös | POC; USB ref 0.5–2.2 |
| `glucose_whole_blood_venous` | mmol/L | Glucose Vollblut venös | POC whole blood; distinct from serum glucose |
| `sodium_whole_blood_venous` | mmol/L | Natrium Vollblut venös | POC BGA; distinct from serum sodium |
| `potassium_whole_blood_venous` | mmol/L | Kalium Vollblut venös | POC BGA; distinct from serum potassium |
| `chloride_whole_blood_venous` | mmol/L | Chlorid Vollblut venös | POC BGA; distinct from serum chloride |

## Hematology

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `hemoglobin` | g/L | Hb, Hgb, Hämoglobin | g/L not g/dL — Switzerland convention |
| `hematocrit` | L/L (or %) | Hct, Hämatokrit | Decimal (0.42) or percent (42) — pick one |
| `wbc` | G/L | Leukocytes, Leukozyten | G/L = ×10⁹/L = thousand/µL |
| `platelets` | G/L | Thrombocytes, Thrombozyten | |
| `mcv` | fL | mean corpuscular volume | |
| `mch` | pg | mean corpuscular hemoglobin | |
| `mchc` | g/L | mean corpuscular hemoglobin concentration | |
| `rdw` | % | EVB, Erythrocyte distribution width | German labs label as EVB |
| `hypochromic_ec` | % | hypochrome Ec | iron-deficiency marker |
| `mpv` | fL | MTV, mean platelet volume | German MTV |
| `pdw` | % | TVB, platelet distribution width | German TVB |
| `neutrophils_abs` | G/L | Neutros, Granulozyten, neutrophiles absolut | |
| `neutrophils_pct` | % | Neutrophile relativ | |
| `lymphocytes_abs` | G/L | Lympho absolut | |
| `lymphocytes_pct` | % | Lymphocytes relativ | |
| `monocytes_abs` | G/L | Mono | |
| `monocytes_pct` | % | | |
| `eosinophils_abs` | G/L | Eos | |
| `eosinophils_pct` | % | | |
| `basophils_abs` | G/L | Baso | |
| `basophils_pct` | % | | |

## Inflammation & immunology

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `crp` | mg/L | C-reactive protein | |
| `procalcitonin` | µg/L | PCT | ng/mL = µg/L numerically |
| `albumin` | g/L | Alb | g/L (not g/dL — SI convention) |
| `total_protein` | g/L | TP, Gesamteiweiss | |

## Bone & mineral

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `pth` | pg/mL | Parathormone, Parathyrin | ng/L = pg/mL |
| `vitamin_d_25oh` | nmol/L | 25-OH-Vit D, Calcidiol | ng/mL × 2.5 = nmol/L |
| `alkaline_phosphatase` | U/L | AP, ALP | |

## Transplant-specific

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `tacrolimus_trough` | µg/L | Tac, Prograf, FK506, C0 | µg/L = ng/mL numerically. Always store `time_post_dose` (h) |
| `tacrolimus_post_dose` | µg/L | Tac post-dose, Tac peak, Tac random | NOT a trough. Drawn after dose; not comparable to trough series |
| `ciclosporin_trough` | µg/L | CsA, Sandimmun, C0 | |
| `mycophenolate_auc` | mg·h/L | MPA AUC | |
| `everolimus_trough` | µg/L | Certican | |
| `ebv_pcr` | copies/mL | EBV-DNA, EBV viral load, ebv_dna_quantitative | Lab-specific; store `assay_lab`. USB reports in GEq/mL |
| `cmv_pcr` | IU/mL | CMV-DNA, cmv_dna_quantitative | International units now standard. USB reports in IU/mL |
| `bk_pcr_blood` | copies/mL | BKV blood, BKPyV blood | |
| `bk_pcr_urine` | copies/mL | BKV urine, BKPyV quan. (Urin), bkpyv_dna_urine | USB reports in GEq/mL; detection limit typically 1000 |
| `jcpyv_dna_urine` | GEq/mL | JCPyV quan. (Urin) | USB detection limit 1000 GEq/mL |
| `respiratory_panel_pcr` | qualitative | Respiratorische Panel PCR | 20-pathogen multiplex, nasopharyngeal swab. Store as "all negative" or list positive targets |

## Liver

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `alt` | U/L | GPT, SGPT, ALAT | |
| `ast` | U/L | GOT, SGOT, ASAT | |
| `ggt` | U/L | γ-GT, Gamma-GT | |
| `bilirubin_total` | µmol/L | Bili, Gesamtbilirubin | |
| `ldh` | U/L | Lactat Dehydrogenase, Lactate Dehydrogenase | Affected by hemolysis |
| `amylase` | U/L | total amylase | Includes salivary + pancreatic |
| `pancreas_amylase` | U/L | Pankreas-Amylase, P-amylase | More specific for pancreas |

## Urine

### Dipstick (qualitative)

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `urine_ph` | (unitless) | | |
| `urine_specific_gravity` | (unitless) | spez. Gewicht | typical 1.005–1.030 |
| `urine_protein_dipstick` | qualitative | | values: negativ / positiv / +/++/+++ |
| `urine_glucose_dipstick` | qualitative | | |
| `urine_ketones_dipstick` | qualitative | Ketokörper | |
| `urine_bilirubin_dipstick` | qualitative | | |
| `urine_nitrite_dipstick` | qualitative | Nitrit | suggests bacteriuria |
| `urine_leukocyte_esterase` | qualitative | | dipstick — confirm with flowcytometry |
| `urine_hemoglobin_dipstick` | qualitative | | |

### Flowcytometry (quantitative microscopy)

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `urine_leukocytes_count` | /µL | | |
| `urine_erythrocytes_count` | /µL | | |
| `urine_bacteria_count` | /µL | | |
| `urine_squamous_epithelium` | /µL | Plattenepithelien | high count suggests contamination |

### Quantitative protein

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `urine_creatinine` | mmol/L | Kreatinin im Urin | for ratio normalization |
| `urine_protein_total` | mg/L | Protein total | spot urine |
| `urine_albumin` | mg/L | | 67 kDa marker, glomerular damage |
| `urine_alpha1_microglobulin` | mg/L | A1M | 33 kDa, tubular damage marker |
| `urine_protein_creatinine_ratio` | mg/mmol | UPCR, Protein/Crea-Quotient | mg/g (US) ÷ 8.84 = mg/mmol |
| `urine_albumin_creatinine_ratio` | mg/mmol | UACR, ACR | KDIGO A1/A2/A3 staging |
| `urine_alpha1_microglobulin_creatinine_ratio` | mg/mmol | A1M/Crea | tubular dysfunction marker |
| `urine_protein_24h` | g/24h | 24h Eiweiss | requires 24h collection |

## Vitamins, hormones, tumor markers

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `vitamin_b12` | pmol/L | Cobalamin | <150 deficiency, 150–300 borderline (check holoTC), >300 sufficient |
| `holotranscobalamin` | pmol/L | holoTC, active B12 | early B12 deficiency marker |
| `folate_serum` | nmol/L | Folsäure (Serum) | |
| `folate_erythrocyte` | nmol/L | Folsäure in den Erythrozyten | better long-term folate status indicator |
| `ferritin` | µg/L | | acute phase reactant |
| `transferrin_saturation` | % | TSAT | iron status |
| `psa` | µg/L | Prostata-spezifisches Antigen | ng/mL = µg/L numerically |
| `tsh` | mIU/L | Thyrotropin | |
| `ft4` | pmol/L | Free T4 | |
| `ft3` | pmol/L | Free T3 | |

## Cardiac & muscle

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `creatine_kinase` | U/L | CK, CPK, Creatinkinase | Skeletal muscle + cardiac + smooth muscle |
| `troponin_t_hs` | ng/L | hs-cTnT, hochsensitives Troponin T | Cardiac specific |
| `troponin_i_hs` | ng/L | hs-cTnI | Cardiac specific |
| `nt_probnp` | ng/L | NT-proBNP | Heart failure marker; rises with reduced eGFR |

## Lipids

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `cholesterol_total` | mmol/L | Cholesterin, total cholesterol | mg/dL × 0.0259 = mmol/L |
| `cholesterol_hdl` | mmol/L | HDL-C | Higher is better |
| `cholesterol_ldl` | mmol/L | LDL-C | Calculated (Friedewald, Sampson) or direct |
| `cholesterol_non_hdl` | mmol/L | non-HDL-C | Total − HDL |
| `cholesterol_hdl_ratio` |  | Chol/HDL | Total / HDL |
| `triglycerides` | mmol/L | Triglyceride, TG | mg/dL × 0.0113 = mmol/L |

## Metabolic

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `glucose` | mmol/L | Blutzucker, BZ, Glucose Notfall | Fasting <5.6, 2h pp <7.8 |
| `hba1c` | "%" | HbA1c, Hämoglobin A1c | Long-term glycemic control |
| `hba1c_mmol` | mmol/mol | HbA1c (IFCC) | IFCC unit; HbA1c% × 10.93 − 23.5 |

## How to add a new analyte

1. Pick a canonical name: lowercase, snake_case, no abbreviations unless universally recognized.
2. Decide on the unit. Use SI; if your lab reports in something else, store the unit explicitly anyway.
3. Add a row here.
4. If it has a population reference range, add it to `reference_ranges.md`.
5. If you have a personal target, add it to `personal_targets.md`.

## Sources for reference values and conversions

- IFCC unit conventions: https://www.ifcc.org/
- Lab Tests Online (patient-facing, well-referenced): https://www.testing.com/
- For transplant-specific assays, the lab's own report is authoritative — assays are not standardized across labs and values are not directly comparable between providers.
- Swiss-specific conventions: SULM (Swiss Union of Laboratory Medicine), https://sulm.ch/

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

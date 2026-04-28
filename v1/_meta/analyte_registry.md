---
type: meta
purpose: analyte_registry
---

# Analyte Registry

Canonical names, units, and aliases. Use the canonical name in YAML frontmatter of lab draw notes. The aliases column is for your reference when transcribing from reports written in German, French, or with non-standard abbreviations.

## Renal & electrolytes

| Canonical | Unit (SI) | Aliases | Notes |
|---|---|---|---|
| `creatinine` | µmol/L | Crea, Kreatinin, Créatinine | Multiply by 0.0113 to get mg/dL |
| `egfr` | mL/min/1.73m² | GFR estimated, eGFR-CKD-EPI | Always store `formula` field |
| `urea` | mmol/L | BUN (US, mg/dL), Harnstoff | BUN mg/dL = urea mmol/L × 2.8 |
| `cystatin_c` | mg/L | Cys-C | Alternative GFR marker |
| `sodium` | mmol/L | Na, Natrium | |
| `potassium` | mmol/L | K, Kalium | Hemolysis falsely raises K |
| `chloride` | mmol/L | Cl, Chlorid | |
| `bicarbonate` | mmol/L | HCO3, Bikarbonat | Often labeled "Standardbicarbonat" on BGA |
| `calcium_total` | mmol/L | Ca, Kalzium | Albumin-correction may be needed |
| `calcium_ionized` | mmol/L | Ca²⁺, ionized Ca | More accurate in low-albumin states |
| `phosphate` | mmol/L | PO4, Phosphat, P | |
| `magnesium` | mmol/L | Mg, Magnesium | |

## Acid-base & blood gas

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `ph` | (unitless) | pH | Venous vs. arterial differs |
| `pco2` | kPa | pCO2 | Switzerland uses kPa; US uses mmHg (×7.5) |
| `po2` | kPa | pO2 | |
| `base_excess` | mmol/L | BE, ABE | |

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
| `ciclosporin_trough` | µg/L | CsA, Sandimmun, C0 | |
| `mycophenolate_auc` | mg·h/L | MPA AUC | |
| `everolimus_trough` | µg/L | Certican | |
| `ebv_pcr` | copies/mL | EBV-DNA, EBV viral load | Lab-specific; store `assay_lab` |
| `cmv_pcr` | IU/mL | CMV-DNA | International units now standard |
| `bk_pcr_blood` | copies/mL | BKV blood | |
| `bk_pcr_urine` | copies/mL | BKV urine | |

## Liver

| Canonical | Unit | Aliases | Notes |
|---|---|---|---|
| `alt` | U/L | GPT, SGPT, ALAT | |
| `ast` | U/L | GOT, SGOT, ASAT | |
| `ggt` | U/L | γ-GT, Gamma-GT | |
| `bilirubin_total` | µmol/L | Bili, Gesamtbilirubin | |

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

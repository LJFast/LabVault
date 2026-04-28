<%*
const studyDate = await tp.system.prompt("Study date (YYYY-MM-DD)", tp.date.now("YYYY-MM-DD"));
const indication = await tp.system.prompt("Indication (short)", "surveillance");

const targetName = `${studyDate}-TTE`;
if (tp.file.title.startsWith("Untitled")) {
  await tp.file.rename(targetName);
}
-%>
---
type: imaging
modality: TTE
study_date: <% studyDate %>
indication: <% indication %>
quality:
rhythm:
heart_rate_bpm:
bp_systolic:
bp_diastolic:

# Structured measurements — fill in only what's reported.
# Leave blank or comment out lines for measurements not provided in your report.
measurements:
  # Left ventricle
  - parameter: lvedd
    value:
    unit: mm
    note: "LV end-diastolic diameter"

  - parameter: lvesd
    value:
    unit: mm
    note: "LV end-systolic diameter"

  - parameter: septum_thickness
    value:
    unit: mm

  - parameter: posterior_wall_thickness
    value:
    unit: mm

  - parameter: lv_mass_index
    value:
    unit: g/m²
    note: "indexed to body surface area"

  - parameter: relative_wall_thickness
    value:
    unit: ratio

  - parameter: lvedv
    value:
    unit: mL
    note: "biplane Simpson"

  - parameter: lvedv_index
    value:
    unit: mL/m²

  - parameter: lvef
    value:
    unit: "%"
    method: "biplane Simpson"

  # Diastolic function
  - parameter: e_wave
    value:
    unit: cm/s

  - parameter: a_wave
    value:
    unit: cm/s

  - parameter: e_a_ratio
    value:
    unit: ratio

  - parameter: e_prime_medial
    value:
    unit: cm/s

  - parameter: e_prime_lateral
    value:
    unit: cm/s

  - parameter: e_over_e_prime
    value:
    unit: ratio
    note: "average of medial and lateral"

  - parameter: deceleration_time
    value:
    unit: ms

  # Left atrium
  - parameter: la_diameter
    value:
    unit: mm

  - parameter: la_volume
    value:
    unit: mL

  - parameter: la_volume_index
    value:
    unit: mL/m²
    note: "≥34 indicates dilatation"

  # Aortic valve & root
  - parameter: aortic_valve_morphology
    value: "tricuspid"   # or bicuspid

  - parameter: aortic_valve_peak_gradient
    value:
    unit: mmHg

  - parameter: aortic_valve_mean_gradient
    value:
    unit: mmHg

  - parameter: aortic_valve_vmax
    value:
    unit: m/s

  - parameter: aortic_regurgitation
    value:           # none / trace / mild / moderate / severe

  - parameter: aortic_root_sinus
    value:
    unit: mm

  - parameter: aortic_root_ascending
    value:
    unit: mm

  # Mitral, tricuspid, pulmonary
  - parameter: mitral_regurgitation
    value:           # none / mild / moderate / severe

  - parameter: tricuspid_regurgitation
    value:

  - parameter: tr_velocity_max
    value:
    unit: m/s
    note: "for PASP estimate; n/a if no TR"

  - parameter: pasp_estimated
    value:
    unit: mmHg

  # Right heart
  - parameter: rv_function
    value:           # normal / impaired

  - parameter: tapse
    value:
    unit: mm
    note: "TAM in some reports; normal >17"

  # IVC
  - parameter: ivc_diameter
    value:
    unit: mm

  - parameter: cvp_estimated
    value:
    unit: mmHg

  # Pericardium
  - parameter: pericardial_effusion
    value:           # none / physiological / mild / moderate / severe
  - parameter: pericardial_effusion_volume
    value:
    unit: mL

# Categorical findings (free text per section)
findings:
  lv: ""
  rv: ""
  atria: ""
  valves: ""
  pericardium: ""
  aorta: ""

beurteilung_summary: ""

clinician: ""
---

# TTE — <% studyDate %>

## Indication

<% indication %>

## Findings narrative (transcribed)

<!-- Paste the structural narrative if you want the prose alongside the structured data.
     DO NOT include patient name, DOB, MRN, or accession numbers. -->

## Beurteilung (impression)

<!-- The cardiologist's overall assessment, transcribed without identifiers. -->

## My notes / questions

<!-- Comparisons to prior, questions for next visit, things to track. -->

## Source

<!-- PDF filename (kept locally outside this vault). -->

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

<%*
const studyDate = await tp.system.prompt("Study date (YYYY-MM-DD)", tp.date.now("YYYY-MM-DD"));
const modality = await tp.system.suggester(
  ["Ultrasound", "CT", "MRI", "X-ray", "Nuclear medicine", "Other"],
  ["US", "CT", "MRI", "XR", "NM", "Other"]
);
const region = await tp.system.prompt("Region (short, e.g., 'kidneys', 'thorax')", "kidneys");

const targetName = `${studyDate}-${modality}-${region.replace(/\s+/g, "_")}`;
if (tp.file.title.startsWith("Untitled")) {
  await tp.file.rename(targetName);
}
-%>
---
type: imaging
study_date: <% studyDate %>
modality: <% modality %>
region: <% region %>
indication:
contrast:
radiologist:
---

# <% modality %> <% region %> — <% studyDate %>

## Indication

<!-- Why was this study ordered? -->

## Findings (transcribed narrative)

<!--
Paste or transcribe the radiologist's findings text here.
DO NOT include patient name, DOB, MRN, or accession number.
You can usually copy the findings text from the patient portal in plain text form.
-->

## Impression

<!-- Radiologist's summary impression -->

## My notes

<!-- Questions for follow-up, comparisons to prior, clinical correlation -->

## Source

<!-- Reference to source PDF filename (kept outside this vault) -->

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

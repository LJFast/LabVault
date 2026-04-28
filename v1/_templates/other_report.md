<%*
const reportDate = await tp.system.prompt("Report date (YYYY-MM-DD)", tp.date.now("YYYY-MM-DD"));
const reportType = await tp.system.suggester(
  ["Consult letter", "Biopsy report", "Discharge summary", "Procedure note", "Other"],
  ["consult", "biopsy", "discharge", "procedure", "other"]
);
const specialty = await tp.system.prompt("Specialty (e.g., 'nephrology', 'cardiology')", "nephrology");

const targetName = `${reportDate}-${reportType}-${specialty}`;
if (tp.file.title.startsWith("Untitled")) {
  await tp.file.rename(targetName);
}
-%>
---
type: report
report_type: <% reportType %>
report_date: <% reportDate %>
specialty: <% specialty %>
clinician:
---

# <% reportType %> — <% specialty %> — <% reportDate %>

## Reason for consultation / procedure

## Summary of report (your transcription, identifiers removed)

## Recommendations / plan

## My notes

## Source

<!-- Reference to source PDF filename (kept outside this vault) -->

---
*Drafted with AI assistance (Claude, Anthropic) under human oversight. Verify against source documents and applicable standards before use.*

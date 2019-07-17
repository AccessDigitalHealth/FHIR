<!--<div xmlns="http://www.w3.org/1999/xhtml" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://hl7.org/fhir ../../src-generated/schemas/fhir-single.xsd">
  <a name="transformation considerations"> </a>-->

###Transformation Considerations

    These are notes



<!--- Text entered into this file will appear at the top of the profiles page before the Formal Views of the profile content. -->

This profile was generated from [HL7 StructureDefinition](https://www.hl7.org/fhir/diagnosticreport.profile.json)

Key differences from [DiagnosticReport Base Resource](https://www.hl7.org/fhir/diagnosticreport.html):
- Removed DiagnosticReport.basedOn from the profile
- DiagnosticReport.status:
  - Change: Made a must-support element 'someinline'
  - Binding: Bound to DiagnosticReportStatus inherited from base resource
- DiagnosticReport.category
DiagnosticReport.code
DiagnosticReport.subject
DiagnosticReport.encounter
DiagnosticReport.effective
DiagnosticReport.effectivePeriod
DiagnosticReport.issued
DiagnosticReport.performer
DiagnosticReport.resultsInterpreter
DiagnosticReport.specimen
DiagnosticReport.result
DiagnosticReport.imagingStudy
DiagnosticReport.media
DiagnosticReport.conclusion
DiagnosticReport.conclusionCode
DiagnosticReport.presentedForm


Key differences from [USCoreR4 Patient](http://www.hl7.org/fhir/us/core/StructureDefinition-us-core-diagnosticreport-lab.html):

- XYZ
- XYS
- XYS

**Note:** Jurisdictional Health Number (JHN) - Version Code Extension

The Version Code is current captured separately from the JHN because, in Ontario at least, the JHN is a stable identifier whereas the version code changes over time.

- The working assumption is that it is useful to have this stable identifier but not all of the Ontario specs reviewed stored it as a separate field.  In one case, it appears to be an Patient.identifier.coding.value instead of the identifier ...
- Example JHN and Patient Information from [Ontario Health Care Validation Reference Manual](http://www.health.gov.on.ca/english/providers/pub/ohip/ohipvalid_manual/ohipvalid_manual.pdf)
- Question: would specific examples like this be helpful to illustrate Canadian requirements?

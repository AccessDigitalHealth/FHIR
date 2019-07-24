# Introduction

This profile was generated from [HL7 MedicationRequest StructureDefinition](https://www.hl7.org/fhir/medicationrequest.profile.json)

## Scope


### Sources-To-Date

- Nova Scotia Pharmacy Vendor Implementation Guide

- New Brunswick DIS Vendor Implementation Guide

- Newfoundland & Labrador Pharmacy Network Vendor Implementation Standards

- Prince Edward Island Pharmaceutical Information Program Integration & Conformance Specification



### Constraints
As part of the scope of the discovery phase of this project, mappings, profiles and implementation guides were expected to be agnostic with regard to implementation decisions or architectural paradigm.
- It could not be assumed that future implementations will exchange information via a RESTful API.
- Design for how for how resources could be identified and verified/matched across organizations and jurisdictions fell outside of scope for this phase. Mappings had to be made under the most basic assumption that resources could be referenced on a local server.

This profile is informed by the Drug Information System vendor specifications from all four Atlantic Provinces. While vendor specifications provide helpful insight into system configurations, test messages and documentation on known variances from the CeRX standard are foundational in accessing each jurisdiction's future conformance to the FHIR profile.

*Supported CeRX interaction types limitations:*
New Brunswick DIS does not currently support query interactions, because of this an alternative CeRX message type (Record Dispense Processing Request) was utilized for both MedicationRequest and MedicationDispense. No alternative CeRX message type could be found for New Brunswick that supported Medication Profile/Statement information, so New Brunswick mappings were excluded from this profile.

*Test message limitations:*
Test messages were only available from one DIS (Prince Edward Island).
  - Further examination of test and pre-production messages will be critical for validating each jurisdiction’s conformance to the standard, as even slight variation can create errors in extraction and risks in conformance to the profile.

*Documented variance limitations:*
Only two jurisdictions (Newfoundland Labrador and New Brunswick) called out specific differences from the CeRx standard in their implementations. None of the differences identified by these sources impacted the interactions that were examined for conversion.
 - While this might imply that conversion from CeRx to FHIR in Atlantic provinces could be accomplished in a broadly standardized manner across jurisdictions, it is important to note that the absence of noted variances in the other two jurisdictions is not the same as confirmation that variances do not exist.

**Note**
Additional evaluation and iteration of these profiles, as additional jurisdictional specifications are made available, is paramount to ensure the final profile reflects the current state of Atlantic systems.


## Key Differences
### **Key differences from [MedicationRequest Base Resource](https://www.hl7.org/fhir/medicationrequest.html):**

- Removed the following elements from the profile due to lack of available semantically equivalent CeRX fields across examined sites:
  - intent, category, priority, encounter, supportingInformation, recorder, reasonReference, instantiatesCanonical, instantiatesUri, basedOn, authorReference, dosageInstruction sub-elements (additionalInstruction, patientInstruction, method.

- Flagged the following elements as "Must Support":
  - status, medication, subject, authoredOn, requester, dosageInstruction, dosageInstruction.text, dispenseRequest, dispenseRequest.numberOfRepeatsAllowed, dispenseRequest.quantity, dispenseRequest.expectedSupplyDuration 

- Changed the cardinality of the following elements:
  - *N/A - no cardinality changes*

### **Key differences from [ACCESS MedicationRequest Patient Profile](http://infoway-inforoute.ca/fhir/StructureDefinition/access-medicationrequest-pat):**

**Must Support Differences**
- dispenseRequest.expectedSupplyDuration is flagged as a Must Support element in the clinician profile, but is an optional element in the Patient Profile
  - expectedSupplyDuration is likely to support clinician workflows but unlikely to add immediately value to patients that already have information on quantity, repeats, expiration, etc.

**Cardinality Constraint Differences**
*N/A - no cardinality differences between the patient and clinician profile*

**Data Type Constraint Differences**
*N/A - no data type differences between the patient and clinician profile*

**Binding Differences**
*N/A - no binding differences between the patient and clinician profile*

### **Key differences from [US Core MedicationRequest R4 Profile](http://build.fhir.org/ig/HL7/US-Core-R4/StructureDefinition-us-core-medicationrequest.html):**


The ACCESS MedicationRequest (Clinician) Profile and US Core MedicationRequest profiles are fairly similar. Semantic mappings from source data (CeRX messages) were both rich and available which resulted in the ACCESS profiles including many more optional FHIR fields than other profiles (e.g. MedicationStatement).

Differences between the two profiles are due to the natural constraints/variances from the anticipated data sources: CeRX Medication Prescription Detail Query Response (PORX_IN060260CA) & inferred from Record Dispense Processing Request (PORX_IN020190CA).

Differences between the two profiles are as follows:

**Must Support Differences**
- dispenseRequest (and some of its sub-elements) are flagged as Must Support for the ACCESS profile, but not US Core. Through reviews with SMEs, the importance of remaining fill information (that is part of the dispenseRequest) is both available given the source data (CeRX messages) and critical to both clinicians and patients.

**Cardinality Constraint Differences**
- US Core creates a lower cardinality constraint (1..1) on the authoredOn element and the requester element, which the ACCESS profiles do not inherit. Because at least one jurisdiction (New Brunswick) does not support Medication Request CeRX messages (prescription information has to be inferred from Medication Dispense CeRX messages), an authoredOn date and requesting practitioner is not guaranteed for every MedicationRequest that is transformed from CeRX source messages.

**Data Type Constraint Differences**
- US Core references its own profiles (Medication, Patient, Practitioner), while the ACCESS profile references the CA Core Profiles

**Binding Differences**
- US Core offers an extensible binding to Rx Norm Medication Codes, while the ACCESS profile suggests a preferred binding to the value set currently proposed in the CA Core Medication profiles (https://fhir.infoway-inforoute.ca/ValueSet/prescriptionmedicinalproduct)

# Introduction

This profile was generated from [HL7 MedicationStatement StructureDefinition](https://www.hl7.org/fhir/medicationstatement.profile.json)

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


## Profiling Principles

**The ACCESS medication profiles were drafted with the following principles in mind:**

***Principle 1: Delineate what information is critical to citizens vs clinicians***
In consultation with subject matter experts, it was determined that citizens and clinicians likely have substantially different information needs, and therefore separate profiles targeting the two groups were required. This profile focuses on the information that is critical for clinicians to interact with a patient's medication list. While the profile focuses primarily on the viewing of medication lists, it was made flexible enough to support future uses cases like citizen & clinician create and update medication statement.

***Principle 2: Target lossless two-way information flow***
CeRX interactions have been heavily adopted by drug information systems in the Atlantic provinces and across Canada. Given the level of adoption of CeRX to exchange medication information, FHIR efforts will likely involve some transformation of source data into target FHIR elements. As applications and systems begin to adopt the FHIR profiles, it's also expected that FHIR resources may also need to be considered for transformation into target CeRX interactions (to reduce burden on drug information systems that may not be ready to transition to FHIR).

In order to ensure adoptability of the profiles in an ecosystem that will support both standards, efforts were made to preserve at least the possibility of lossless two-way exchange. Lossless two-way exchange depends in part on architectural decisions that are outside the scope of this project (and is therefore not guaranteed), however efforts were made to outline key considerations and challenges for performing conversions between FHIR -> CeRX and CeRX -> FHIR. Those considerations can be found in the Transformation Considerations Page (insert link here).

***Principle 3: Target alignment with US Core and Canadian Core profiles***
Medication profiles were designed with alignment (or at least compatibility) with US Core in mind. US Core R4 Profiles were targeted for two reasons:
  1. Existing adoption by US-based vendors, and
  2. Their use in the efforts to establish a Canadian Core profiles

This principle was adopted with the goal to enable broader interoperability and avoid proliferation of competing standards and profiles.

***Principle 4: Ensure constraint decisions are source-aware***
Medication profiles prioritized FHIR elements that map to CeRX fields that are currently available across drug information systems and jurisdictions. Given that jurisdictions are currently storing and exchanging much of the medication information using CeRx interactions, FHIR efforts will likely involve some transformation of source data into target FHIR elements.

The majority of critical/must-support elements were covered in the existing mapping of CeRX elements to FHIR elements, however some drug information systems could only support CeRX interactions that were limited in what they supported for data elements. Establishing FHIR cardinality constraints impact source systems by increasing risk of conformance errors in cases where the CeRX standard varies from the FHIR profile:
  - Example 1: CeRX allows an element to be optional or populated with a null value, but FHIR profile requires at least one value to be present (1..X FHIR cardinality)
  - Example 2: CeRX allows an element to have multiple repeats, but the FHIR profile only allows one value (X..1 FHIR cardinality).

These types of CeRx cases had a major influence on the decision to not introduce additional cardinality constraints to the medication resources. Given that the ACCESS profiles will need to be flexible enough to support all Canadian jurisdictions, additional cardinality constraints are not recommended until more is known about jurisdictional implementations of CeRx outside the Atlantic provinces.

This approach maintains optionality and flexibility in the FHIR profiles and circumvents the requirement for drug information systems to have to dramatically modify their existing implementations of CeRX.


## Change Classification Legend
        Must Support = Sending and receiving applications must be able to recognize and respond to elements flagged as must support. They may not always have data available for those elements but they are required to recognize them.

        Optional = A FHIR element or sub-element that is not considered a must support element, but is kept in the profile because there is a semantic mapping available at source sites **AND** it is applicable to patients/citizens

        Removed from Profile = A FHIR element or sub-element that is not considered a must support element, use is **not** prohibited but it is also not encouraged for any reason due to a) applicability to citizens b) availability at source sites, or c) both.

        Prohibited (Strikethrough) = A FHIR cardinality that if sent results in a conformance error. *Given the goal to avoid over-constraining the profiles, no elements were identified as prohibited in any of the profiles*

## Key Differences
### **Key differences from [MedicationStatement Base Resource](https://www.hl7.org/fhir/medicationstatement.html):**

- Removed the following elements from the profile due to lack of available semantically equivalent CeRX fields across examined sites:
  - basedOn, partOf, statusReason, category, context, derivedFrom, reasonReference, and note

- Flagged the following elements as "Must Support":
  - status, medication, subject, effective, dateAsserted, informationSource, and reasonCode

- Changed the cardinality of the following elements:
  - *N/A - no cardinality changes*

### **Key differences from [ACCESS MedicationStatement Patient Profile](http://infoway-inforoute.ca/fhir/StructureDefinition/access-medicationstatement-pat):**

**Must Support Differences**
- dateAsserted is flagged as a Must Support element for ACCESS Medication Statement Clinician Profile, but is only noted as optional in the patient-focused profile. This was due to SME recommendation, given that the date of statement assertion was primarily valuable for clinicians performing medication management & reconciliation.
  - *Note: This element does not have an airtight mapping to a CeRX element and may need to be ascertained from metadata (message creation time) in the wrapper of the query/response*
- informationSource is flagged as a Must Support element for ACCESS Medication Statement Clinician Profile, but is only noted as optional in the patient-focused profile.
  - Source and verifiability of the medication statement impact clinical decision particularly if medicationStatements support patient & caregiver creation use cases.

**Cardinality Constraint Differences**
*N/A - no cardinality differences between the patient and clinician profile*

**Data Type Constraint Differences**
*N/A - no data type differences between the patient and clinician profile*

**Binding Differences**
*N/A - no binding differences between the patient and clinician profile*

### **Key differences from [US Core MedicationStatement R4 Profile](http://build.fhir.org/ig/HL7/US-Core-R4/StructureDefinition-us-core-medicationstatement.html):**

Much like MedicationRequest, the ACCESS MedicationStatement profile has intentional alignment with the US Core MedicationStatement profile.

Because both profiles are focused on clinician access, differences between the two profiles are largely due to natural constraints and variances from the anticipated data sources (CeRX Medication Profile Summary Query Response: PORX_IN060400CA).

Differences between the two profiles are as follows:

**Must Support Differences**
- derivedFrom is flagged as a Must Support element for US Core, but is not in the ACCESS profile. This was due to the available source data (CeRX messages) only supporting boolean values for whether or not the statement was derived from a secondary source.
- informationSource is flagged as a Must Support Element for this ACCESS profile. While it is not the same as the derivedFrom element, informationSource was included as a must-support because it allowed Clinicians more information to help validate the MedicationStatement when the clinician isn't the source of truth for the resource.
- reasonCode is flagged as a Must Support element for ACCESS profile, but not US Core. The availability of observationDiagnosis/Symptom/Indications in the CeRX messages makes this a reasonable value to expect from implementors.
  - reasonCode was marked as must support in the clinician profile to support medication management activities and enrich the medication review conversations between patient and clinician when the clinician is not the source of the medicationStatement.

**Cardinality Constraint Differences**
- US Core creates a lower cardinality constraint on the dateAsserted element (1..1), however CeRX semantic mapping limitations for dateAsserted the ACCESS profile did not take on the same 1..1 cardinality put forth by the US Core.

**Data Type Constraint Differences**
- US Core binds to its own profile for Medication and Patient, while the ACCESS profile references the CA Core Medication and Patient Profiles.

**Binding Differences**
- US Core offers an extensible binding to Rx Norm Medication Codes, while the ACCESS profile suggests a preferred binding to the value set currently proposed in the CA Core Medication profiles (https://fhir.infoway-inforoute.ca/ValueSet/prescriptionmedicinalproduct)


## Implementor Notes
**Note:**ACCESS

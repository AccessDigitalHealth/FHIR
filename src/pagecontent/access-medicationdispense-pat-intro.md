# Introduction

This profile was generated from [HL7 MedicationDispense StructureDefinition](https://www.hl7.org/fhir/medicationdispense.profile.json)

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
New Brunswick DIS does not currently support query interactions, because of this an alternative CeRX message type (Record Dispense Processing Request) was utilized for both MedicationRequest and MedicationDispense.

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
In consultation with subject matter experts, it was determined that citizens and clinicians likely have substantially different information needs, and therefore separate profiles targeting the two groups were required. This profile focuses on the information that is critical for citizens/patients to understand their medications that have been dispensed or filled. This profile focuses primarily on the viewing of medication dispense information, it was made flexible enough to support clinicians creating and updating medicationDispense resources.

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

This approach maintains optionality and flexibility in the FHIR profiles and circumvents the requirement for drug information systems to  dramatically modify their existing implementations of CeRX.


## Change Classification Legend
        Must Support = Sending and receiving applications must be able to recognize and respond to elements flagged as must support. They may not always have data available for those elements but they are required to recognize them.

        Optional = A FHIR element or sub-element that is not considered a must support element, but is kept in the profile because there is a semantic mapping available at source sites **AND** it is applicable to patients/citizens

        Removed from Profile = A FHIR element or sub-element that is not considered a must support element, use is **not** prohibited but it is also not encouraged for any reason due to a) applicability to citizens b) availability at source sites, or c) both.

        Prohibited (Strikethrough) = A FHIR cardinality that if sent results in a conformance error. *Given the goal to avoid over-constraining the profiles, no elements were identified as prohibited in any of the profiles*

## Key Differences
### **Key differences from [MedicationDispense Base Resource](https://www.hl7.org/fhir/medicationdispense.html):**

- Removed the following elements from the profile due to lack of available semantically equivalent CeRX fields across examined sites:
  - partOf, statusReason, category, context, supportingInformation, performer, whenPrepared, whenHandedOver, destination, receiver, note, substitution.responsibleParty, and eventHistory

- Flagged the following elements as "Must Support":
  - status, medication, subject, location, quantity, daysSupply, and dosageInstruction

- Changed the cardinality of the following elements:
  - dosageInstruction.text


### **Key differences from [ACCESS MedicationDispense Clinician Profile](http://infoway-inforoute.ca/fhir/StructureDefinition/access-medicationdispense-clin):**

**Must Support Differences**
*N/A - no cardinality differences between the patient and clinician profile. There was desire to make authorizingPrescription a Must-Support element in the clinician profile- however it's data type requirement to reference a FHIR MedicationRequest resource was too restrictive given that there will be prescriptions still received by the DIS in paper form*

**Optional Field Differences**
While the must support elements remain the same for both patient and clinician MedicationDispense profiles, the optional elements and sub-elements supported by each profile are drastically different

The MedicationDispense Patient profile does **not** include the following optional elements that are included in the clinician profile:
- performer, performer.function, performer.actor
  - Reviews with SME indicated that the dispensing pharmacy is more likely to be important to patients and dispensing pharmacy is already covered in the location resource.
  - Some jurisdictions outside of the Atlantic provinces (e.g. Saskatchewan) may not capture the name of the pharmacist in the DIS.
- dosageInstruction sub-elements (timing, site, route, doseAndRate.doseRange, doseAndRate.rateRatio, maxDosePerPeriod)
  - These details were not included in the patient profile on the assumption that the critical information will be communicated in the dosageInstuction.text or dosageInstuction.patientInstruction elements, making these redundant for the use by patients.
- substitution.responsibleParty
  - The practitioner responsible for making a substitution is likely not meaningful for citizens but could be used by clinicians for coordination/verification.

The MedicationDispense Patient profile also includes an optional element under dosageInstruction (patientInstruction) that the clinician profile does not include.
  - Given the focus of these profiles for citizen access, this element was not removed from patient profile. However, there is no clear mapping distinction between patient instructions and dosage instructions in the CeRX specifications so jurisdictional DIS would need to be evaluated before conversion could begin.

**Cardinality Constraint Differences**
*N/A - no cardinality differences between the patient and clinician profile*

**Data Type Constraint Differences**
*N/A - no data type differences between the patient and clinician profile*

**Binding Differences**
*N/A - no binding differences between the patient and clinician profile*

### **Key differences from [US Core MedicationDispense R4 Profile]**

*US Core does not support a profile for MedicationDispense*

### **Key differences from [ONC MedicationDispense STU3 Profile](http://build.fhir.org/ig/Healthedata1/FHIR-ONC-Meds/StructureDefinition-medicationdispense.html)**

Because there was no US Core equivalent profile for MedicationDispense, the ONC profile for MedicationDispense was used as an international source of comparison.

**Must Support Differences**
- The ONC profile flags performer and performer.actor as must support elements, these are not included in the ACCESS MedicationDispense Patient profile (included as optional in the clinician profile).
  - SME Note: Location was preferred over performer for the patient profile because dispensing pharmacy is more likely to be important to patients. Dispensing pharmacy is already covered in the location resource.
  - Some jurisdictions outside of the Atlantic provinces (e.g. Saskatchewan) may not capture the name of the pharmacist in the DIS.
- The ONC profile flags whenHandedOver as a must support element, these are not included in the ACCESS MedicationDispense Patient profile (also not included in the clinician profile).
  - SME Note: Jurisdictional DIS support of the CeRX equivalent for whenPrepared & whenHanded over is likely varied. When a DIS does support the CeRX equivalent, it is more likely only record whenPrepared due to existing workflow issues.  
- The ACCESS patient profile includes location as a Must Support element  
  - location was not present in the ONC profile because location is a new element FHIR R4 and the ONC profile is based in STU 3.
- The ACCESS patient profile includes quantity and daysSupply as Must Support elements (recommendation of SME), the ONC profile does not.

**Cardinality Constraint Differences**
The ACCESS profiles intentionally did not introduce cardinality constraints into the medication profiles (*see Profiling Principle 4 above*).

-The ONC profile created a lower constraint on the cardinality of subject (making it 1..1). Because subject is not readily present in the CeRX Drug Dispense Drug message (PORX_MT060090CA) and instead needs to be inferred from the linked prescription (when it's available). There is no guarantee that the patient information will always be available in the CeRX data source, and therefore the profile could not mirror the cardinality constraint present in the ONC Profile.

**Data Type Constraint Differences**
- The ONC MedicationDispense profile references the US Core Medication Profile, whereas the ACCESS profile references the CA Core Medication Profile.

**Binding Differences**
- The ONC profile offers an extensible binding to Rx Norm Medication Codes, while the ACCESS profile suggests a preferred binding to the value set currently proposed in the CA Core Medication profiles (https://fhir.infoway-inforoute.ca/ValueSet/prescriptionmedicinalproduct).

### **Key differences from [Ontario Digital Health Drug Repository STU3 Profile](https://simplifier.net/ontariodigitalhealth/medicationdispense)**

Because there was no US Core equivalent profile for MedicationDispense, the Ontario Digital Health Drug Repository (DHIR) Profile was used as a Canadian source of comparison.

The ACCESS profile's dependence on HL7v3 CeRX messages as its source data is the primary driver in differences between the DHIR profile and the ACCESS profile.The cardinality restrictions and bindings that DHIR utilizes are not feasible for application in the ACCESS profiles because conversion of CeRX messages into FHIR creates limitations and boundaries in what data will available and populated consistently.

**Must Support Differences**
- The DHIR profile flags identifier, identifier.system, and identifier.value as Must Support elements that are not considered Must Support elements in the ACCESS MedicationDispense Patient Profile
- category, performer, authorizingPrescription, whenHandedOver, detectedIssue are also included as must support elements in the DHIR MedicationDispense Profile that are not flagged as Must Support elements in the ACCESS MedicationDispense Patient Profile.
-The ACCESS patient profile includes location as a Must Support element  
  - location was not present in the DHIR profile because location is a new element FHIR R4 and the DHIR profile is based in STU 3.
- The ACCESS patient profile includes dosageInstruction as a Must Support element (recommendation of SME), the DHIR profile does not.

**Cardinality Constraint Differences**
The ACCESS profiles intentionally did not introduce cardinality constraints into the medication profiles (*see Profiling Principle 4 above*). The DHIR profile includes a number of cardinality differences that the ACCESS profile does not mirror:
- Elements with upper constraint added in DHIR profile: performer (..2), performerOrganization (..1), performerPractitioner (..1), authorizingPrescription (..1)
- Elements with lower constraint added in DHIR profile: codeableConcepts elements (identifier.system, identifier.value, category.system, category.code, category.display), medicationReference, subject, subjectReference, quantity, quantity.value, daysSupply.value, whenHandedOver
- Elements with both upper and lower constraints added in DHIR profile: identifier, category.coding

**Data Type Constraint Differences**
- DHIR constrains the medication data type to only include medicationReference (medicationCodeableConcept removed), the ACCESS profile allows for medicationReference and medicationCodeableConcept to cover any situations where only the coded information is known (medicationReference is kept for situations where a compound is used)
- DHIR uses a specific Patient Dispense resource for subject, while the ACCESS profile references the CA Core Patient Profile which does not distinguish between what interaction the patient is being referenced in.
-DHIR references its own MedicationRequest Profile, ACCESS references its own MedicationRequest Patient Profile (MedicationRequest Clinician Profile is referenced in the clinician-focused dispense profile).
- DHIR references its own profiles for DetectedIssue: DURIntervention, DURResponse, DURTextMessageHNSODB, DURTextMessageNMS, ACCESS references the DetectedIssue Base Resource.

**Binding Differences**
- The DHIR profile requires a binding to the eHealth Ontario MedicationDispenseStatus subset of the STU3 MedicationDispense Status value set, while the ACCESS profile requires the R4 medicationDispense status value set.
- DispenseCategory is used as a preferred binding in the DHIR profile to help distinguish between drugs, devices, and professionals, ACCESS does not include MedicationDispense category because device dispenses and medicationAdministration were considered out of scope of Phase B.
- The ACCESS profile supports an extensible binding for MedicationDispense.type (ActPharmacySupplyType which is a v3 value set), however type is not included in the DHIR profiles
- The ACCESS profile supports preferred bindings for substitution type (V3 Value SetActSubstanceAdminSubstitutionCode) and substitution reason (V3 Value SetSubstanceAdminSubstitutionReason) however type is not included in the DHIR profiles


## Implementor Notes
**Note:**

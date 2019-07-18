# Introduction

This profile was generated from [HL7 Observation StructureDefinition](https://www.hl7.org/fhir/observtion.profile.json)

## Scope


### Sources-To-Date

- Nova Scotia HL7v2 LIS Specifications & Test Messages
  - Cerner Millennium (NSHA Central Zone)
  - MEDITECH Client Server (NSHA Northern, Eastern, and Western Zones)
  - MEDITECH Magic (IWK Health Centre)

- Prince Edward Island Cerner Millennium HL7v2 LIS Specification

- Ontario Laboratory Information System (OLIS) HL7v2 LIS Specification


### Constraints
As part of the scope of the discovery phase of this project, mappings, profiles and implementation guides were expected to be agnostic with regard to implementation decisions or architectural paradigm.
- It could not be assumed that future implementations will exchange information via a RESTful API.
- Design for how for how resources could be identified and verified/matched across organizations and jurisdictions fell outside of scope for this phase. Mappings had to be made under the most basic assumption that resources could be referenced on a local server.

Availability of Atlantic LIS specifications and test messages were also a limitation on mapping and profiling efforts. Currently, the general lab profiles were completed using documentation from Nova Scotia, Prince Edward Island, and Ontario Lab Information Systems (LIS). Additional evaluation and iteration of these profiles, as additional jurisdictional specifications are made available, is paramount to ensure the final profile reflects the current state of Atlantic systems.

Alignment and compatibility with other prominent profiles in similar functional areas was sought (e.g. OLIS, US Core/CA Core), in part to validate the ACCESS Atlantic work, and in part to ensure that decisions taken in developing the ACCESS Atlantic profiles were, at a minimum, not mutually exclusive with widely used profiles.


## Profiling Principles

**The lab profiles were drafted with the following principles in mind:**

***Principle 1: Focus on general lab results***
Prioritize the FHIR elements that are critical to enhancing citizen access and interaction with their lab result information. While the medication profiles offer a profile for citizen access and a profile for clinician access, existing clinician viewers/interfaces already exist for lab information.

***Principle 2: Focus on general lab results***
  - In the examined LIS specifications, the message structure for microbiology, pathology, serology, and blood bank vary significantly from how the messages are formatted for general labs.
    - Typically in the Nova Scotia &  Prince Edward Island LIS examples, pathology, microbiology, and serology were rendered/dictated text reports with each line of the report represented as an OBX-5 value (many OBX lines), while general labs had more structured data throughout the OBR and OBX fields.
    - Without data existing in structured known locations, it's very difficult to ensure that the data is mapped to the correct FHIR element.
  - Additionally, Subject Matter Expert (SME) review indicated that general labs should be prioritized as they offer the most value for early citizen access use cases.

***Principle 3: Target alignment with US Core, CA Core, and OLIS profile***
- The DiagnosticReport and Observation profiles were designed with alignment (or at least compatibility) with existing implementations in mind. The goal was to enable pan-Canadian interoperability while avoiding a proliferation of competing and mutually incompatible standard or profiles.
  - The profiles targeted the US Core R4 Profiles due to their existing adoption as well as their use in the efforts to establish a Canadian Core profiles
  - The lab focused profiles targeted OLIS profiles and specification given their maturity and current adoption.

***Principle 3: Ensure constraint decisions are source-aware***
- Focus on FHIR elements that map to HL7v2 ORU fields that are available across LIS and jurisdictions.
  - Given that jurisdictions are currently storing and exchanging much of the lab information using HL7 v2 messages, FHIR efforts will likely involve some transformation of source data into target FHIR elements.

## Change Classification Legend
      Must Support = Sending and receiving applications must be able to recognize and respond to elements flagged as must support. They may not always have data available for those elements but they are required to recognize them.

      Optional = A FHIR element or sub-element that is not considered a must support element, but is kept in the profile because there is a semantic mapping available at source sites **AND** it is applicable to patients/citizens

      Removed from Profile = A FHIR element or sub-element that is not considered a must support element, use is **not** prohibited but it is also not encouraged for any reason due to a) applicability to citizens b) availability at source sites, or c) both.

      Prohibited (Strikethrough) = A FHIR cardinality that if sent results in a conformance error. *Given the goal to avoid over-constraining the profiles, no elements were identified as prohibited in any of the profiles*

## Key Differences
### **Key differences from [Observation Base Resource](https://www.hl7.org/fhir/observation.html):**

- Removed the following elements from the profile due to lack of available semantically equivalent ORU fields across examined sites:
  -  basedOn, focus, encounter, effectivePeriod/Timing/Instant, bodySite, method, device, referenceRangeLow/High/Type/Age, hasMember, derivedFrom, component

- Flagged the following elements as "Must Support":
  -status, category, code, subject, effectiveDateTime, value, dataAbsentReason, interpretation

- Changed the cardinality of the following elements:
  -category, subject

These profiles sought to target alignment with the US Core Lab Result Observation Profile and OLIS Observation Profile in order to avoid proliferation of competing standards.

### **Key differences from [US Core Lab Result Observation R4 Profile](https://build.fhir.org/ig/HL7/US-Core-R4/StructureDefinition-us-core-observation-lab.html):**

The ACCESS Atlantic and US Core Observation profiles are quite similar. The US Core Profiles are being used as the foundation to draft the Canadian Core Profiles. Because of this, the ACCESS Atlantic Laboratory and Medication profiles sought to align with the US Core whenever appropriatewith a few exceptions.

It is important to note that US Core profiles in STU3 and R4 have mild differences between them (ex: cardinality for category), the best efforts were made to ensure optionality while the Canadian FHIR working groups identify which choices will be inherited into the CA Core profiles.  

Variances between the ACCESS Atlantic and US Core are minimal. Resources that are created by a system that utilizes the US Core are very likely to be conformant to the ACCESS Atlantic profile, so long as it supports interpretation (not necessary to populate). Because the ACCESS profiles do not implement prohibitive constraints, even if a resource was to include effectivePeriod (a variance between the two profiles) it should not result in a conformance error.

**Must Support Differences**
- Interpretation was flagged as a must-support element in the ACCESS Atlantic profile, but not in the US Core Profile. The flag was added due to the importance of interpretive data for citizens’ understanding of their lab results compared to the reference range

**Cardinality Constraint Differences**
- ACCESS Atlantic Observation profile set the cardinality of category to 1..*  while US Core R4 sets an upper cardinality of 1..1 for category, US CORE STU3 utilizes a 1..* cardinality. Until the CA Core profiling community determines which of the versions Canadian vendors are more likely to align with, the cardinality should be kept at 1..* to avoid over constraining the profiles and introducing conformance errors.

**Cardinality Constraint Differences**
- ACCESS Atlantic Observation profile limited the effective[x] data type to only include effectiveDateTime. While the base resource supports multiple data types: DateTime, Timing, Period, Instant, the lab sources are expected to be utilizing HL7 v2 time stamp data type which more closely aligns with dateTime. *OLIS took a similar approach to its observation profile, while US Core allowed both dateTime and Period data types to be supported.*  
- The ACCESS Atlantic profile also binds to the CA Core profiles whenever possible (CA Core Patient, practitioner, organization).

**Binding Differences**
- Setting a fixed system or value for category was avoided due to the expectation that other profiling efforts will result in the use of two (or more) category systems and values. US Core fixes its system to “http://terminology.hl7.org/CodeSystem/observation-category” and fixes its value to “laboratory”.  *While to OLIS has a preferred binding to http://hl7.org/fhir/STU3/codesystem-observation-category.html and suggests the use of “laboratory” and “exam” values.*
- In observationCode, US Core uses an extensible binding to LOINC Code value set. The ACCESS Atlantic profile provides an alternative example binding to the lab observation code value set that is utilized in the OLIS profile


### **Key differences from the [OLIS DiagnosticReport STU3 Profile](https://simplifier.net/ontariolaboratoriesi/diagnosticreport-provider):**

The ACCESS Atlantic and OLIS Observation profiles are broadly similar – ACCESS Atlantic requirements are effectively a subset of OLIS requirements, however OLIS is more restrictive in the observation profile particularly involving must-support elements and cardinality. Differences between the two profiles are as follows.

It is important to note that the OLIS profiles at the time of this analysis are built on FHIR STU3, while ACCESS profiles are built on FHIR R4. A number of variances are not due to design choice, but rather are the result of the changes of the base resource due to FHIR version. Furthermore, the ACCESS profiles are tailored to citizen access (unlike the OLIS profile examined on Simplifier) and it is believed to be likely that citizens/patients require different details than clinicians – e.g. a citizen or patient’s interpretation of the data is not likely impacted by the method by which a laboratory test was performed.

Version differences notwithstanding, any implementation that adheres to the OLIS profile should also be compatible with the ACCESS Atlantic profile, so long as it also supports dataAbsentReason and provides a defaulted value for category (*defaulting discussed further in the gap analysis excel files and the profile change logs*) .


**Must Support Differences**
- dataAbsentReason is flagged as Must Support for ACCESS Atlantic, but not OLIS.
  - This is due to the focus that the ACCESS Atlantic laboratory profiles have on patient access.
  - dataAbsentReason was added as a must support element in order to reduce confusion in cases where a lab result is not available.
- id, identifier, basedOn, issued, performer, method, specimen and referenceRange are flagged as Must Support for OLIS but not ACCESS Atlantic.
  - This was done in accordance with the profiling principles to avoid over-restriction and align with the US/CA Core whenever possible.
  - While referenceRange is anticipated to be available in most resources, reviews with subject matter experts (SMEs) revealed that reference ranges will vary by the lab and testing methods utilized by the data source. The elements that are likely to provide the most meaning to citizens are lab value and value interpretation (which are both tagged as must supports).


**Cardinality Constraint Differences**
- identifier, basedOn, category, issued, performer, value, interpretation, and referenceRange have different cardinalities between the two profiles which can be categorized in the following ways:
  - Elements with upper constraint added in OLIS profile: interpretation, referenceRange, category
  - Elements with lower constraint added in OLIS profile: issued, value
  - Elements with both upper & lower constraints added in OLIS profile: identifier, basedOn, performer
  - Elements with lower constraint added in ACCESS profile: category


**Data Type Constraint Differences**
- The OLIS Observation profile constrains subject to the OLIS Patient Profile, while the ACCESS profile references the CA Core Patient Profile in anticipation of its release
- The ACCESS Observation profile similarly limits performer data types to Practitioner and Organization references, however each references it’s respective profiles (CA Core & OLIS)


**Binding Differences**
- Observation Status is one example of binding variances between OLIS and ACCESS Atlantic Profiles. OLIS suggests a preferred binding to the STU3 Observation Status Value Set, while the ACCESS profile inherits the required binding from the R4 base resource (Observation Status).
  - While the value sets are different, the coded values within both value sets are the same, so no terminology considerations are anticipated.
- Interpretation value set binding is another example of differences between the two profiles. ACCESS promotes an extensible binding to the R4 Observation Interpretation Value Set, while the OLIS profile uses the eHealth Ontario published value set tied to the binding from the STU3 base resource (Observation Interpretation)
  - While the value sets are different, the codes that OLIS utilizes are a subset of the code system that the ACCESS profile is bound to.


## Implementor Notes
**Note:**

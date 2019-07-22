# Introduction

This profile was generated from [HL7 DiagnosticReport StructureDefinition](https://www.hl7.org/fhir/diagnosticreport.profile.json)

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

Alignment and compatibility with other prominent profiles in similar functional areas was sought (e.g. OLIS, US Core/CA Core), in part to validate the ACCESS Atlantic work, and in part to ensure that decisions taken in developing the ACCESS profiles were, at a minimum, not mutually exclusive with widely used profiles.


## Profiling Principles

**The lab profiles were drafted with the following principles in mind:**

***Principle 1: Focus on citizen access***
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

***Principle 4: Ensure constraint decisions are source-aware***
- Focus on FHIR elements that map to HL7v2 ORU fields that are available across LIS and jurisdictions.
  - Given that jurisdictions are currently storing and exchanging much of the lab information using HL7 v2 messages, FHIR efforts will likely involve some transformation of source data into target FHIR elements.

## Change Classification Legend
        Must Support = Sending and receiving applications must be able to recognize and respond to elements flagged as must support. They may not always have data available for those elements but they are required to recognize them.

        Optional = A FHIR element or sub-element that is not considered a must support element, but is kept in the profile because there is a semantic mapping available at source sites **AND** it is applicable to patients/citizens

        Removed from Profile = A FHIR element or sub-element that is not considered a must support element, use is **not** prohibited but it is also not encouraged for any reason due to a) applicability to citizens b) availability at source sites, or c) both.

        Prohibited (Strikethrough) = A FHIR cardinality that if sent results in a conformance error. *Given the goal to avoid over-constraining the profiles, no elements were identified as prohibited in any of the profiles*

## Key Differences
### **Key differences from [DiagnosticReport Base Resource](https://www.hl7.org/fhir/diagnosticreport.html):**

- Removed the following elements from the profile due to lack of available semantically equivalent ORU fields across examined sites:
  - basedOn, encounter, resultsInterpreter, imagingStudy, media, presentedForm

- Flagged the following elements as "Must Support":
  - status, category, code, subject, effectiveDateTime, issued, performer, result

- Changed the cardinality of the following elements:
  - category, subject

These profiles sought to target alignment with the US Core DiagnosticReport Profile and OLIS DiagnosticReport Profile in order to avoid proliferation of competing standards.

### **Key differences from [US Core DiagnosticReport R4 Profile](https://build.fhir.org/ig/HL7/US-Core-R4/StructureDefinition-us-core-diagnosticreport-lab.html):**

Much like Observation, the ACCESS DiagnosticReport profile has intentional alignment with the US Core DiagnosticReport profile.
Differences between the two profiles are largely due to the citizen access focus of the ACCESS profile as well as from natural constraints and variances from the anticipated data sources (HL7 v2 ORU RO1 messages).  

Differences between the two profiles are as follows:

**Must Support Differences**
- Media and presentedForm elements are flagged as Must Support elements in the US Core profile, but are not considered Must Support in the ACCESS profile.
  - Media: This decision was due to the difficulty associated with correctly identifying and parsing the correct media from an HL7 v2 ORU RO1 attachment when there is no clear method of differentiating between what the base-64 encoded images are intended to represent when they are included in the OBX-5 field of a message.
  - presentedForm: While providing the full ORU message in the presentedForm element helps clinicians ensure clinical fidelity, this profile is solely focused on providing simple meaningful lab information to patients in a user-friendly manner that can be displayed by third-party applications. Very few patients are expected to know how to read the ORU messages presented and the variances between LIS formatting of messages will likely cause further confusion.

**Cardinality Constraint Differences**
- Category, effective, and issued have different cardinalities between the two profiles which can be categorized in the following ways:
  - Elements with upper constraint added in US Core profile: category
  - Elements with lower constraint added in US Core profile: effective, issued

**Cardinality Constraint Differences**
- ACCESS DiagnosticReport profile limited the effective[x] data type to only include effectiveDateTime. While the base resource supports multiple data types: DateTime, Timing, Period, Instant, the lab sources are expected to be utilizing HL7 v2 time stamp data type which more closely aligns with dateTime. US Core allows dateTime and Period data types.
- The ACCESS profile also binds to the CA Core profiles whenever possible (CA Core Patient, organization) and the ACCESS general lab observation profile.

**Binding Differences**
- Setting a fixed system or value for category was avoided due to the expectation that other profiling efforts will result in the use of two (or more) category systems and values. US Core fixes its system to “http://terminology.hl7.org/CodeSystem/v2-0074” and fixes its value to “LAB”.  ACCESS provides an example binding from the same code system but allows for additional diagnostic service categories to be present in the value set since that additional detail may be present in OBR-24 of the source HL7 v2 messages.
- For diagnosticReportCode, US Core creates an extensible binding to its own value set: US Core Diagnostic Report Laboratory Codes which pull from the LOINC code system. Because the LIS in the analysis primarily use their own unique nomenclatures (with some jurisdictions like PEI and OLIS supporting LOINC codes or nomenclatures that map to LOINC codes) the ACCESS profile identified the LOINC codes as a preferred binding when available but did not require it’s use through a “required” or “extensible” conformance binding.
- The ACCESS DiagnosticReport profile supports the optional elements of conclusionCode and provides an example binding to SNOMED CT clinical findings.


### **Key differences from the [OLIS DiagnosticReport STU3 Profile](https://simplifier.net/ontariolaboratoriesi/diagnosticreport-provider):**

Much like Observation, the ACCESS profile and OLIS DiagnosticReport profiles are similar; Observation ACCESS profile requirements are an approximate subset of OLIS requirements. It's important to note that OLIS profiles are built on FHIR STU3, while ACCESS profiles are built on FHIR R4, so a one-to-one comparison is not possible. Furthermore, the ACCESS profiles are tailored to citizen access, whereas OLIS is intended to support clinician use. Differences between the two profiles are as follows:

**Must Support Differences**
- category and effectiveDateTime are flagged as Must Support for the ACCESS profile and US Core, but not OLIS
- id, contained, identifier, specimen, and codedDiagnosis  are flagged as Must Support for OLIS but not the ACCESS profile.

**Cardinality Constraint Differences**
- identifier, category, performer, and specimen have different cardinalities between the two profiles which can be categorized in the following ways:
  - Elements with upper constraint added in OLIS profile: category, performer, and specimen
  - Elements with lower constraint added in OLIS profile:
  - Elements with both upper and lower constraints added in OLIS profile: identifier
  - Elements with a lower constraint added in the ACCESS profile: category

**Data Type Constraint Differences**
- The OLIS profile constrains subject to the OLIS Patient Profile, while the ACCESS profile references the CA Core Patient Profile in anticipation of its release
- The ACCESS profile similarly limits performer data types to Practitioner and Organization references, however each references it’s respective profiles (CA Core & OLIS)
- In addition to referencing its own Observation profile, OLIS also references a secondary OLIS Observation profile focused on Microbiology. The ACCESS profile in comparison, only focuses on general lab results & diagnostic reports.

**Binding Differences**
- The ACCESS profile binds to the R4 Diagnostic Report Status value set whereas the OLIS profile binds to the STU3 Diagnostic Report Status value set. Similar to the observation profile, the value sets are different, but the coded values within both value sets are the same so no terminology considerations are anticipated.

## Implementor Notes
**Note:**

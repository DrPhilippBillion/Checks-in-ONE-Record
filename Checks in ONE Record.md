# Using Checks in ONE Record

## Basic Information on this document

### Objective 
The purpose of this document is to provide a Good Practice for using Checks in the IATA ONE Record-based data eco-system. 

### Target audience
This document can be used by any party with the interest in the topic. 

### Geographical coverage
As there are no legal or operational restrictions, the solution can be used world wide.

### Creators
This document is the outcome of Lufthansa Cargo´s operational implementation of ONE Record in cooperation with the "Digitales Testfeld Air Cargo" by the German air cargo community. Parties/Persons involved were:

Lufthansa Cargo, Philipp Billion

Lufthansa Cargo, Ingo Zeschky

Lufthansa Industry Solutions, Daniel Döppner

### Continous development and availability

This document is to be used and continously developed, even if the current major stakeholders should move to other topics. Thus a "handover" in Github is planned if responsibilities should shift.

### Use and reference

This Good Practice is free to access and use. If you use it, please refer to this document explicitly plus provide a link to the Github repository as source. This will ensure know-how-transfer and transparency.

### Publication date, version and history

Publication date, version and history should be provided by the Github version control system and not be duplicated here.

## Dependencies

### Standards applied

The ONE Record business ontology version as of APR 13, 2022 was used [Working draft Ontology of 2022APR13](https://github.com/IATA-Cargo/ONE-Record/blob/bbe86e364b04d6a6279f0ab6e9ee47e1905ec9c4/working_draft/ontology/IATA-1R-DM-Ontology.ttl).

The ONE Record API and security specification draft witout a version as of APR 13, 2022 was used (no link available yet).

### ONE Record Server Implementation used

(no ONE Record server implementation yet)

### Other Software products used

(no other software yet)

## Assumptions

A central assumption is that there is an interest of stakeholders in documenting and communicating check results digitally.

## Solution approach

The ONE Record data model follows two principles: Piece-centricity and physics-orientation. Piece-centricity is self-explanatory, as every information is linked with the piece as the lowest available transportation object. Physics-orientation means that the linking strucutre of ONE Record follows the structures of the physical world (for more details, please refer to [ONE Record Github Repository](https://github.com/IATA-Cargo/ONE-Record).

_Checks_ have a very broad definition here. It can include:

* Checks of single physical objects, like the manual check of a ULD temperature as displayed on the outside or a countour check for a pallet.
* Checks of non-physical objects, like manual or automatic data content checks on AWB-relevant data.
* Acceptance checks including a mixed setting of physical and non-physical objects, like the Ready-for-carriage export acceptance check.
* Specialized commodity checks like the DG acceptance check.
* Any other check related with physical or data objects.

Checks can be performed manually and automatically. 

The reading of IOT-Data is not a check. If IOT-data is provided, an assumption is that this data is checked permanently against limitations, so implementing each data submission as a check would result in a high number of checks without additional information benefits. Only if IOT data is read manually, this can bei implemented as a check. 

## Solution in current environment

In the legacy messaging environment, sharing digital check sheets and their results isn´t possible. Generic solutions might be in place, but - to my knowledge - no data exchange beyond the scans of check sheets exists. 

## Benefits of new approach

Digital Checks in ONE Record create the following benefits
* real-time management of checks and check sheet templates
* real-time transparency on check results
* machine-readable transparency on check results even on a most ranular level enabling application of data analytics and machine learning


# Data use and target process

As a high-level approach, the checks data objects are linked into the object to be checked, meaning that a ULD-related check is linked directly into the ULD, the check of a piece is directly linked into the piece, an automated address contour-check is linked with into the address object.

The identification of the "target" object for the check might be tricky, as some checks include a mix of physical and data objects, like the DG acceptance check. Both, the DGD and the physical freight are checked here. One "clean" solution could be to separate the physical and the documentary acceptance check, linking the physical check to the pieces, and the documentary check to the DGD. Another solution is to include more than one object into the check, meaning in practice to link many objects into the check data object. The disadvantage is that a high number of linked objects of very different types can be quite confusing to manage. Thus a recommended solution is to prefer linking the check objects to a lower level of aggregated objects.

The basic approach for checks is a separation of check template provider and check executer. This separation is optional, of course, but makes most sense in the current environment. E.g. the DG Check sheet template is usually provided by a carrier or IATA, while GHA staff is performing the check. This separation is enabled by the data model implementation.

The following diagram shows the relevant data fields in the ONE Record data model:

![DataModel](diagrams/dm.svg) 

## referring LO

As the check data objects can be linked into the object to be checked, practically any checked object can contain a link to the check. Each item can have no, one or many linked checks, and each check can be linked to one or many objects.

### Data field: check

The ***check*** data field is optional and contains a URI linking the ***checkURI*** in the check LO. Example: "1r.aviation-services.com/DG-acceptance-checks/FRA/020-55522664"

## check LO

### Data field: checkURI

The ***checkURI*** is the unique ID for a check LO.

### Data field: checkTemplate

The ***checkTemplate*** contains the link to a check template LO. Example: "1r.lufthansa-cargo.com/templates/current/DG-acceptance-check"

### Data field: checkTotalResult

The ***checkTotalResult*** contains the link to a total result LO. Example: "1r.aviation-services.com/DG-acceptance-checks/FRA/020-55522664/result"

### Data field: CheckType

The ***checkType*** is a string and contains a description of the check type. Example: "DG acceptance check"

### Data field: checker

The ***checker*** is of type person and links the person performing the complete check. Example: "1r.aviation-services.com/staff/MikeTyson".

### Data field: checkDate

The ***checkDate*** is the date/timestamp, when the check was finalized. Example: "_?std. timestamp_".

### Data field: checkLocation

The ***checkLocation*** is a link into a location object and indicates the execution location of the complete check. Example: "1r.aviation-services.com/warehouses/FRA".

### Data field: checkExternalReference

The ***checkExternalReference*** is a link into an externalRerefence that has a relation with the complete check. This could e.g. be a photo taken during check. Example: "1r.aviation-services.com/photos/08651465". This should not contain e.g. a pdf copy of the completed check sheet, as this is to be provided in the totalResults LO.

## checkTotalResult LO

The checkTotalResult LO should be provided by the party executing and accounting for the check result.

### Data field: checkURI

This data field contains the backlink to the check LO.

### Data field: resultPassed

***resultPassed*** is a string text field with possible options "yes", "no" and "N/A". Example: "yes".

### Data field: resultValue

If the total result of a check provides a value, if can be shared in this data field of type value with the two components ***value*** and ***unit***. If e.g. the check is a temperature as manually read from a device, it would be "5° Celsius". But even in this case, a prefered solution would be to use a template with only one question on the current temperature and one answer with the actual temperature.

### Data field: resultRemarks

***resultRemarks*** is a string text field that can contain any free text remarks on the total result. Example: "Delayed check due to delayed arrival of aircraft".

### Data field: resultCertifiedBy

Only if the checker does not sign responsible for the text result, the ***resultCertifiedBy*** must link a person in charge of the check result, if required at all. Example: "1r.aviation-services.com/staff/JimMiller".

### Data field: resultExternalReference

The ***resultExternalReference*** is a link into an externalRerefence that has a relation with the check result. This could e.g. be a generated PDF with a scanned signature of the paper check sheet version. Example: "1r.aviation-services.com/DG-acceptance-checks/FRA/020-55522664/result/PDF"

## checkTemplate LO

The checkTemplate LO should be provided by the party in charge of the template used for the check. This can be e.g. the carrier or even IATA. As a template contains one or more questions, the questions are a separate data object here.

### Data field: checkTemplateURI

The ***checkTemplateURI*** is the unique ID of the template. Example: "1r.lufthansa-cargo.com/templates/current/DG-acceptance-check".

### Data field: templateQuestion

The ***templateQuestion*** links one or more objects of type Question to the checkTemplate. Example: "1r.lufthansa-cargo.com/templates/questions/NumberOfPieces".

### Data field: templateUses

The ***templateUses*** is the backlink of the template to the check. It is effectively a list of checks, where the template was used.

### Data field: templateName

The ***templateName*** is a string containing the name of the template. Example: "Lufthansa Cargo DG Acceptance Check Sheet".

### Data field: templatePurpose

The ***templatePurpose*** is a string containing a description of the purpose of the template. Example: "DG Acceptance Check Sheet".

### Data field: templateVersion

The ***templateVersion*** is a string containing a version number of the template. Examples: "Version 2022" or "v2.0".

### Data field: templateDate

The ***templateDate*** is a date/timestamp containing the release point in time of the template. 

### Data field: legacyTemplate

The ***legacyTemplate*** is an externalReference linking a photo or pdf of the original paper version of a check template. 

### Data field: templateOtherPartyInvolved

The ***templateOtherPartyInvolved*** is linking an object of type Party that has a stake in the template. Example: "1r.IATA.org".

## question LO

Each question LO provides one question of the template used for the check. This can be provided e.g. the carrier or even IATA. Each question must have a correllating answer, provided by the executioner of the check.

### Data field: questionURI

The ***questionURI*** is the unique ID of the question. Example: "1r.lufthansa-cargo.com/templates/questions/NumberOfPieces".

### Data field: usedInTemplate

The ***usedInTemplate*** is the backlink of the question in one or more check templates. 

### Data field: questionNumber

The ***questionNumber*** is a string containing the number of the specific question. It is a string, not an integer, because often numbering systems like "1.5" or "1a" are used. It should be used in a way that a standard ascending sorting algorithm will sort it in a way as intended by the provider. Example: "1.5".

### Data field: questionShortText

The ***questionShortText*** is a string containing a short text of the question. Example: "Number of pieces correct?".

### Data field: questionLongText

The ***questionLongText*** is a string containing an extended text of the question. Example: "is the number of pieces correctly indicated in the DGD and correllating with the physics?". The use of the short text is mandatory, while the long text is optional.

### Data field: questionSection

If the questions are clustered into sections, the section of the specific question should be indicated into the ***questionSection***. This is a string. Example: "Documentary Check".

### Data field: answerOptionText

If the possible answers should be limited to one or more specific text options, this can be indicated here. The different options are to be separated by a _comma_. Example: "yes, no, N/A".

### Data field: answerOptionValue

If the possible answers should be limited to one or more specific value options, this can be indicated here. The different options are to be separated by a _comma_. Example: "0. 1, 2, 3". If units are required, or text should be included, the answerOptionText is to be used instead, e.g. with "1°, 2°, more than 2°".

## answer LO

Each answer LO provides one answer for each question of the template used for the check. The answer should be provided by the party performing the check. 

### Data field: answerURI

The ***answerURI*** is the unique ID of the answer. Example: "1r.aviation-services.com/DG-acceptance-checks/FRA/020-55522664/result/answerQuestion1".

### Data field: answerText

The ***answerText*** is a string provided as response to a question. Example: "N/A".

### Data field: answerValue

The ***answerValue*** is a value provided as response to a question. It is of type value and thus consists of value and unit. Example: "5° celsius".

### Data field: answerLocation

The ***answerLocation*** is a location provided to inform the data consumer where the linked question was answered. This is especially relevant if checks are split into a physical and a documentary part, performed at different locations. It can also serve a additional authentication feature when e.g. connected with a geolocation sensor of a device. It is of type location. Example: "IATA Service Center Madrid".

### Data field: answerPerson

The ***answerPerson*** is refering to a natural person to inform the data consumer who answered the question. This is especially relevant if checks are split into a physical and a documentary part, performed different persons. Example: "1r.aviation-services.com/staff/MikeTyson".

### Data field: answerExternalReference

The ***answerExternalReference*** is a link to a BLOB with a connection to the answer of the question. This could for example be a foto of a piece, if it was damaged and the status was asked in the question. Example: "1r.aviation-services.com/photos/546468882".

### Data field: answerOtherParty

The ***answerOtherParty***can link another party of relevance to the answer of the question. This could for example be an external provider executing the check. It should only be used if this party is different to the providing party of the answer. Example: "1r.aviation-services-london.com".

## Additional comments / FAQs

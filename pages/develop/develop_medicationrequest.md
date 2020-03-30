---
title: MedicationRequest
keywords: design
tags: [design]
sidebar: overview_sidebar
permalink: develop_medicationrequest.html
summary: Implementation guidance for populating and consuming the FHIR MedicationRequest resource
---

## Introduction

Here

### Overarching principles

Here

### Minimum Viable Product (MVP)

Elements marked as **MVP** denote those recommended to be required for an MVP for the target use case.

| Common Elements |  | CareConnect Extension |  | New in R4 |  | Removed from R4 |  |
| -- | -- | -- | -- | -- | -- | -- | -- |  -- | -- |
| **id** | **MVP** | repeatInformation |  | statusReason |  | context |  | 
| text |  | statusReason |  | doNotPerform |  | definition |  | 
| identifier |  | prescriptionType |  | reported[x] |  | requester. agent |  | 
| **status** | **MVP** | | | encounter |  | requester. onBehalfOf |  |
| **intent** | **MVP** | | | performer |  | | |
| **category** | **MVP** | | | performerType |  |  | |
| priority |  | | |  instantiatesCanonical |  |  | |
| **medicationReference** | **MVP** | | | instantiatesUri |  |  | |
| **subject** | **MVP** | | | courseOfTherapyType |  |  | |
| supportingInformation |  | | | insurance |  |  | |
| **authoredOn** | **MVP** | | | dispenseRequest. initialFill |  |  | |
| **requester** | **MVP** | | | dispenseRequest. initialFill.quantity |  |  | |
| recorder |  | | | dispenseRequest. initialFill.duration |  |  | |
| reasonCode |  |  | | dispenseRequest. dispenseInterval |  |  | |
| reasonReference |  |  | | dispenseRequest. numberOfRepeatsAllowed |  |  | |
| note |  | | | | |
| **dosageInstruction** | **MVP** |  | | | |
| dispenseRequest |  |  | | | |
| **substitution** | **MVP** |  | | | |
| priorPrescription |  |  | | | |

## MedicationRequest elements

### id

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Id</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Required 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Unique logical identifier for the resource.</td>
  </tr>
</table>

It is highly recommended that the logical id value is a Universally Unique Identifier (UUID) using a standard UUID generator available in most programming languages.

The most comprehensive current definition of the logical id is within the FHIR [R4](https://hl7.org/fhir/R4/resource.html#Resource) standard. The key phrase from the FHIR standard is the logical id is "...*assigned by the server responsible for storing it*".

Vendors have advised that there could be several different scenarios where a MedicationRequest could be stored and it is an implementation decision about where the master MedicationRequest should reside. Possible scenarios for the master MedicationRequest location include;
- ePMA system FHIR Server
- Dispensing system FHIR Server
- Centrally hosted FHIR server (inside hospital network)
- Centrally hosted FHIR server (cloud hosted) **Note**: This would be the scenario when the NHS Electronic Prescription Service implements the FHIR standard.

A further implementation decision is who creates the logical id. The following is in line with the [STU3 FHIR RESTful standards](http://hl7.org/fhir/STU3/http.html#update) and [R4 FHIR RESTful standards](https://hl7.org/fhir/R4/http.html).
-  **Server**:  If the hosting system creates the logical id then the client system would send the MedicationRequest (without an id) as a REST POST command. The created logical id should always be included within the HTTP 201 response so the client can persist this id. For centrally hosted FHIR servers it should always be the hosting system the creates the id so that the server can be responsible for logical id uniqueness.
-  **Client**:  If the client system creates the logical id then the client system would send the MedicationRequest (with an id) as a REST PUT command. The REST PUT command will update a record, but if it does not exist create a new record. If the client system sends the MedicationRequest resource via an asynchronous interoperability pattern then the logical id must be populated with a UUID by the client system. An example would be sending a MedicationRequest within a FHIR payload via the NHS Digital MESH platform.

If either a client or server wants to use an additional business identifier this would be added as an MedicationRequest.identifier and the minimum requirement is:
- A system value: defining the source of the system it has come from
- A value: giving the identifier value

If a client system creates the logical id and there are multiple clients sending MedicationRequests there is a possibility a duplicate logical id occurs. In this event the duplicate id (the second code received) will be rejected with a response stating the logical id is duplicated. The requesting system will need to send the request again with a new logical identifier.

For this reason, within an implementation where multiple clients are POSTing to a FHIR server, it is highly recommenced that the FHIR server creates the logical id to remove the risk of duplication.

<hr/>

### identifier

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Identifier</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>External ids for this request. Identifiers associated with this medication request that are defined by business processes and/or used to refer to it when a direct URL reference to the resource itself is not appropriate. They are business identifiers assigned to this resource by the performer or other systems and remain constant as the resource is updated and propagates from server to server.</td>
  </tr>
</table>

Guidance TBC.

### text

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Narrative</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Text summary of the resource, for human interpretation</td>
  </tr>
</table>

Guidance TBC.

<hr/>

### status

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Code</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Mandatory 1..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>A code specifying the current state of the medication order.</td>
  </tr>
</table>

If populated must use a fixed value set defined within the FHIR standard.

It is expected that most implementations will require the use of **status** to support workflow. 

For the purposes of this guidance, the scope of **status** extends to dispensing and administration events. 

| Status | FHIR Definition | Implementation Guidance |
| -- | -- | -- | -- |
| `Draft`  | The prescription is not yet 'actionable', e.g. it is a work in progress, requires sign-off, verification or needs to be run through decision support process. | The order is work in progress within the ePMA system and has not yet sent to the pharmacy. |
| `Active` | The prescription is 'actionable', but not all actions that are implied by it have occurred yet. | The order has been sent and accepted by the pharmacy. Dispensing and administration activities may of started but are not yet complete. |
| `Completed`  | All actions that are implied by the prescription have occurred. | Dispensing and administration activities have been completed for the medication defined within the order. |
| `On Hold` | Actions implied by the prescription are to be temporarily halted, but are expected to continue later. May also be called 'suspended'. | Will prevent the order being sent to the pharmacy. If already sent, an update needs to be sent to the pharmacy to temporarily halt further dispensing. |
| `Cancelled` | The prescription has been withdrawn before any administrations have occurred. | Will prevent the order being sent to the pharmacy. If already sent, an update needs to be sent to the pharmacy so that no further medication is dispensed. |
| `Stopped` | Actions implied by the prescription are to be permanently halted, before all of the administrations occurred. This should not be used if the original order was entered in error. | The order needs to be stopped on clinical grounds. An update needs to be sent to the pharmacy so that no further medication is dispensed. |
| `Entered in Error` | Some of the actions that are implied by the medication request may have occurred. For example, the medication may have been dispensed and the patient may have taken some of the medication. Clinical decision support systems should take this status into account. | The order needs to be stopped due to human data entry error. An update needs to be sent to the pharmacy so that no further medication is dispensed. |
| `Unknown` | The authoring/source system does not know which of the status values currently applies for this observation. Note: This concept is not to be used for 'other' - one of the listed statuses is presumed to apply, but the authoring/source system does not know which. | Recommended not to be supported as the use case for this status value is unclear. |

#### Status Transitions 

![Status Transitions](images/medicationrequest_status_diagram.jpg)

| Previous Status | Future Status | Interoperability Guidance |
| -- | -- | -- |
| `Draft` | `Active` | This transition will trigger the sending/sharing of the MedicationRequest from the ePMA system to the pharmacy system to start dispensing activities. Within a RESTful implementation this would be typically implemented as an HTTP POST. |
| `Draft` | `Cancelled` | Contained within the ePMA system. |
| `Draft` | `On-Hold` | Contained within the ePMA system. |
| `On-Hold` | `Draft` | Contained within the ePMA system. |
| `On-Hold` | `Active` | This transition will trigger an update to the MedicationRequest from the ePMA system to the pharmacy system to restart dispensing activities. Within a RESTful implementation this would be typically implemented as either an HTTP PUT or PATCH. |
| `Active` | `Active` | Not a MedicationRequest status transition but the pharmacy system would send/share dispensing activities with the ePMA system, typically using a FHIR profile based on **MedicationDispense**. Within a RESTful implementation this would be typically implemented as an HTTP POST. |
| `Active` | `On-Hold` | This transition will trigger an update to the MedicationRequest from the ePMA system to the pharmacy system to suspend dispensing activities. Within a RESTful implementation this would be typically implemented as either an HTTP PUT or PATCH. |
| `Active` | `Entered in Error` | This transition will trigger an update to the MedicationRequest from the ePMA system to the pharmacy system to stop dispensing activities. Within a RESTful implementation this would be typically implemented as either an HTTP PUT or PATCH. |
| `Active` | `Stopped` | This transition will trigger an update to the MedicationRequest from the ePMA system to the pharmacy system to stop dispensing activities. Within a RESTful implementation this would be typically implemented as either an HTTP PUT or PATCH. |
| `Active` | `Completed` | Contained within the ePMA system. All dispensing activity has been received from the pharmacy system within **MedicationDispense** FHIR resources. The ePMA system has completed the recorded of medicine administration events. |

<hr/>

### intent

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>code</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Mandatory 1..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code>  <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Describes the nature of the medication request.</td>
  </tr>
</table>

The value `order` should always be used to denote this is a medication request order.

FHIR R4 extends the value set to; `proposal`, `plan`, `order`, `original-order`, `reflex-order`, `filler-order`, `instance-order` and `option`, but the recommendation for the target use case is to continue to use `order` unless it is locally decided that the extended R4 value set better supports the business requirements.

<hr/>

### category

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>CodeableConcept</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Required 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code>  <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Type of medication usage. Indicates the type of medication request. For example, where the medication is expected to be consumed or administered, i.e. inpatient or outpatient.</td>
  </tr>
</table>

It is expected that any implementation will need to distinguish between medication orders for processes for dispensing and/or administration so this element is **business required**. 

The STU3 suggested value-set is defined as; `inpatient`, `outpatient` and `community`. The R4 suggested value-set is extended with *discharge*. For a UK implementation, it is recommended to further extend with `leave`.

The business meaning for some **Category** values for a UK implementation differs from the FHIR international standard definitions. The suggested value-set applicable for implementation within the UK is as follows;

| Category | Implementation Guidance |
| -- | -- |
| `inpatient` | Requests for medications to be administered on a hospital ward or another acute care setting including Accident and Emergency (A&E). Typically requests would be dispensed by the **hospital pharmacy** and administered to the patient on the ward. |
| `outpatient` | Requests for medication from an outpatient clinic. Typically requests would be dispensed by the **hospital pharmacy** for the patient to self-administer at home. |
| `community` | Requests for medication to be dispensed by a community pharmacy (akin to an NHS FP10HP paper prescription) or other dispenser outside the hospital, such as a Homecare medicines provider. **Note**: This category is likely to be used for all medication requests from Primary Care (e.g. GPs) to community pharmacies or Dispensing Appliance Contractors (akin to an NHS FP10 paper prescription). |
| `discharge` | Requests for medication the patient will take away with them on discharge from an inpatient stay. Typically requests would be dispensed by the **hospital pharmacy** for the patient to self-administer at home. |
| `leave` | Requests for medications that the patient will take away with them during any short break from inpatient care. Typically requests would be dispensed by the **hospital pharmacy** to be self-administered at home with or without the assistance of community based nursing staff. |

For the target ePMA to hospital pharmacy systems use case, it would be expected the the ePMA system is capable to creating medication requests for all five categories.

The only category that does not trigger the sending/sharing of a FHIR medicationRequest resource with the hospital pharmacy would be a `community` medication request. A `community` medication request would either trigger the printing and signing of a paper FP10HP prescription, or (when implemented by the Trust) an electronic prescription sent to the NHS Electronic Prescription Service.

<hr/>

### priority

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Code</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Indicates how quickly the medication request should be addressed with respect to other requests.</td>
  </tr>
</table>

Recommended **not** to be used within an implementation or used **with caution**.

The stating of a priority, in any business context including healthcare, is often de-valued as given the choice, every clinician wants medication urgently for their patients.

The FHIR standard uses a fixed value-set of `routine`, `urgent`, `asap` and `stat`, which denote a priority in increasing orders of magnitude, with `stat` being the highest possible priority, e.g. an emergency.

The use of the term `stat` is potentially confusing as when used within a dosage instruction can also mean "*give once immediately*".

If to be used, consider only initially supporting `routine` and `urgent` and set clear criteria for when a medication request should be marked as `urgent`.

<hr/>

### medicationReference

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Mandatory 1..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Medication requested to be dispensed.</td>
  </tr>
</table>

The method by which the Medication resource is linked will be a local implementation decision. There are three options;

 1. Referenced by URL to a FHIR Server
 2. Referenced by an identifier to a Medication resource within the same FHIR Bundle
 3. Referenced by an identifier to a "contained" Medication resource within the MedicationRequest resource

FHIR snippets using XML notation are as follows;

    <!-- 1. by URL -->
    <MedicationRequest>
	    <medicationReference>
	    	<reference value="https://acmefhirserver/medication/87652004"/>
	    	<display value="Atenolol"/>
	    </medicationReference>
    </MedicationRequest>
    
    <!-- 2. by identifier within the Bundle -->
    <Bundle>
	    <entry>
		    <fullUrl value="medication-87652004"/>
		    <resource>
			    <Medication>
				    <!-- medication details for Atenolol -->
			    </Medication>
		    </resource>
		</entry>
		<entry>
			<resource>
			    <MedicationRequest>
				    <medicationReference>
					    <reference value="#medication-87652004"/>
					    <display value="Atenolol"/>
					</medicationReference>
				</MedicationRequest>
			</resource>
		</entry>
    </Bundle>
    
    <!-- 3. by identifier to a contained resource -->
    <MedicationRequest>
	    <contained>
		    <Medication>
			    <id value="medication-87652004"/>
			    <!-- medication details for Atenolol -->
		    </Medication>
	    </contained>
	    <<medicationReference>>
		    <reference value="#medication-87652004"/>
		    <display value="Atenolol"/>
		</<medicationReference>>
	</MedicationRequest>

It is recommended that an implementation uses a reference by URL to a trusted SNOMED/dm+d FHIR Medication Resource Server. It is recommended that the `reference.display` is populated with the medication description selected by the user.

**Note**: At the time of writing an alpha implementation of a FHIR dm+d server is available from the North East CSU as a [demonstrator](https://dmdsite-uks-test-web.azurewebsites.net/Dosage) and associated [API]([https://apidmd001.azurewebsites.net/index.html](https://apidmd001.azurewebsites.net/index.html)).

Where a trusted FHIR dm+d server is not available or not used within an implementation, the reference to the Medication resource should be within a Bundle.

Q) In this instance the Medication.text can be populated. QUESTION: Are we happy about how the Medication resource is populated, in particular about when the drug name in the system the clinician is using is different to the of dm+d?

The use of a contained Medication resource should be the last option considered. Note that when a Medication resource is contained inside the MedicationRequest resource the `Medication.text` element **should not be populated**. This is because the Medication resource is contained inside a MedicationRequest resource and all text should be represented in the `MedicationRequest.text` element, including data from the contained Medication resource.

<hr/>

### subject

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Mandatory 1..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>A link to a resource representing the person or set of individuals to whom the medication will be given. </td>
  </tr>
</table>

The scope of the target use case relates to individual patients only, not groups of patients therefore the linked resource shall only be a FHIR **Patient** resource. Refer to the [Patient](develop_patient.html) resource implementation guidance.

The method by which the Patient resource is linked will be a local implementation decision. There are three options;

 1. Referenced by URL to a FHIR Server
 2. Referenced by an identifier to a Patient resource within the same FHIR Bundle
 3. Referenced by an identifier to a "contained" Patient resource within the MedicationRequest resource

FHIR snippets using XML notation are as follows;

    <!-- 1. by URL -->
    <subject>
    	<reference value="https://acmefhirserver/patient/2245386903"/>
    	<display value="Joe Bloggs"/>
    </subject>
    
    <!-- 2. by identifier within the Bundle -->
    <Bundle>
	    <entry>
		    <fullUrl value="patient-2245386903"/>
		    <resource>
			    <Patient>
				    <!-- patient details for Joe Bloggs -->
			    </Patient>
		    </resource>
		</entry>
		<entry>
			<resource>
			    <MedicationRequest>
				    <subject>
					    <reference value="#patient-2245386903"/>
					    <display value="Joe Bloggs"/>
					</subject>
				</MedicationRequest>
			</resource>
		</entry>
    </Bundle>
    
    <!-- 3. by identifier to a contained resource -->
    <MedicationRequest>
	    <contained>
		    <Patient>
			    <id value="patient-2245386903"/>
			    <!-- patient details for Joe Bloggs -->
		    </Patient>
	    </contained>
	    <subject>
		    <reference value="#patient-2245386903"/>
		    <display value="Joe Bloggs"/>
		</subject>
	</MedicationRequest>

It is recommended that an implementation uses a reference by URL to a trusted Patient Administration FHIR Server. It is recommended that the `reference.display` is populated with the full name of the patient.

**Note**: It is acknowledged that a typical Hospital Patient Administration System (PAS) will not expose a FHIR interface so the above option will most likely not be available for some time so is an aspirational recommendation.

Where a trusted Patient Administration FHIR Server is not available or not used within an implementation, the reference to the Patient resource should be within a Bundle.

The use of a contained Patient resource should be the last option considered.

<hr/>

### supportingInformation

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Information to support ordering of the medication.</td>
  </tr>
</table>

The FHIR specification suggests information such as the patient height and weight could be included within this element to support the medication order.

Do we agree?

<hr/>

### authoredOn

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>dateTime</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Required 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The date, and maybe also time, when the prescription was initially written.</td>
  </tr>
</table>

Recommended as a mandatory element for most implementations.

Recommended to specify as a complete date and time, e.g. "2020-03-26T15:00:00".

Recommended that the date and time is the same as recorded and visible within the ePMA system.

<hr/>

### requester

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Required 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The clinician requesting the medication.</td>
  </tr>
</table>

Recommended as a required business element for most implementations.

Recommended to be the prescribing clinician recorded on the ePMA system for the medication request, as a reference to a FHIR **Practitioner** resource.

The requester can be a reference to a number of different FHIR resources; *Practitioner*, *PractitionerRole*, *Organization*, *Patient*, *RelatedPerson* or *Device*. For this use case it is recommended to always use **Practitioner** unless an implementation supports use cases like requests direct from patients or automated requests from medical or monitoring devices.

#### Additional Guidance

Where an implementation does not currently record the prescribing clinician then consider the following;
- If the prescribing clinician is authorising new medication then populate with their details.
- If the prescribing clinician is re-ordering from a previous medication request that they authorised then populate with their details, and consider populating **priorPrescription**.
- If the prescribing clinician is acting on behalf of a colleague to authorise new medication then populate with the details of the colleague and consider also populating **recorder**.
- If the prescribing clinician is re-ordering from a previous medication request authorised by a colleague then populate with their details (not the colleagues) and consider populating **priorPrescription**.
- If the prescribing clinician is acting on behalf of a colleague to re-order from a previous medication request authorised by someone else then populate with the details of the colleague and consider also populating **recorder** and **priorPrescription**.

**Note**: If the user is not a qualified prescriber but performing the task of data entry then they are instead acting as the **recorder**.

**TBC**: What if a nurse is requesting more supply of a drug from an original prescription?

**Note**: STU3 allowed a request on behalf of another organisation to be defined using **requester.onBehalfOf**. This has been removed from R4 so is recommended not to be used within any STU3 or CareConnect implementation.

<hr/>

### recorder

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The user who entered the data entry to record the medication request.</td>
  </tr>
</table>

Optional for most implementations and requires all system users to be individual authenticated. The primary purpose of capturing the recorder would be for the local audit trail. The data recorder will be of little relevance for most pharmacy implementations.

<hr/>

### reasonCode

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>CodeableConcept</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Reason or indication for requesting the medication for the patient.</td>
  </tr>
</table>

Optional but useful to the wider clinical team as an additional safety check, especially if the requested medication is normally prescribed for different reasons, to avoid confusion between clinical teams. Recording an indication against a medication request also gives valuable insight when data is collated for secondary uses, especially if linked with outcome data.

Where possible this should be a coded term from the SNOMED-CT hierarchy as a descendant of the concept 404684003 (Clinical finding) however but free-text reasons are also acceptable.

FHIR 4 has several additional structures here?

<hr/>

### reasonReference

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Condition or observation that supports why the prescription is being written.</td>
  </tr>
</table>

Guidance TBC.

<hr/>

### note

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Annotation</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Information about the prescription.</td>
  </tr>
</table>

Can be used to support local requirements not supported elsewhere within the resource. Any data contained can only be processed by a human user. Unless required for local business processes, **do not** include this element.

<hr/>

### dosageInstruction

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Dosage</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Required 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>TBC</td>
  </tr>
</table>

Refer to [FHIR Dose Syntax Implementation Guidance](https://developer.nhs.uk/apis/dose-syntax-implementation-1-3-2-alpha/) (or any subsequent version) for guidance.

<hr/>

### dispenseRequest

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>BackboneElement</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Information related to the dispensing of the medication.</td>
  </tr>
</table>

It is recommended that this structure is **omitted**, unless required data to be shared cannot be appropriately populated elsewhere within the resource.

Many elements within this structure are to support primary care prescribing processes using VMP and AMP dm+d concepts and where a structured dosage instruction is not populated.

Refer to [FHIR Dose Syntax Implementation Guidance](https://developer.nhs.uk/apis/dose-syntax-implementation-1-3-2-alpha/) (or any subsequent version) for further guidance.

<hr/>

### substitution

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>BackboneElement</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Required 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Indicates whether or not substitution can or should be part of the dispense. In some cases, substitution must happen, in other cases substitution must not happen. This block explains the prescriber's intent. If nothing is specified substitution may be done.</td>
  </tr>
</table>

Within UK healthcare, substitution is not the norm so the international FHIR definition where "*If nothing is specified substitution may be done.*" does to align with UK healthcare prescribing best practice.

It could be unwise to assume all UK implementations will prevent substitution if not explicitly stated, especially if the same clinical system has been previously implemented outside the UK. It is therefore recommended that this element is **business required** with a default boolean value of `false` to denote substitution is **not** allowed.

#### Allowing Substitution

Where substitution to be be allowed, set to "**true**". The inclusion of the coded reason is optional as the value-set defined in FHIR is of limited benefit to UK healthcare.

<hr/>

### priorPrescription

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code> <code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>An order/prescription that is being replaced AND a link to a resource representing an earlier order related order or prescription.</td>
  </tr>
</table>

The published FHIR specifications described this element is slightly different ways in different parts of the FHIR specification;

"*A link to a resource representing an earlier order related order or prescription*" 

and 

"*An order/prescription that is being replaced*".

The following guidance applies to each use case.

#### Linking to an earlier order

A medication request that is a repeat of an earlier request would be referenced within **priorPrescription**. This would allow both the ePMA and pharmacy systems to logically link requests and add verification checks to flag any differences to the user.

An order for the same medication but for a different dose can still be linked using **priorPrescription**. In this scenario it would be expected that the prescribing clinician provides **supporting information** for the pharmacy to confirm the change in the medication request.

#### Linking to an order that is being replaced

The medicationRequest being replaced will be referenced within **priorPrescription**. It would be expected that the referenced resource would be updated with a **status** of *cancelled*, *entered-in-error* or *stopped*. This will allow both the ePMA and pharmacy systems to make it clear to the human user that one medication request replaces another.

<hr/>

### extension (repeatInformation)

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Extension</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>CareConnect</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Medication repeat information extension to support the NHS Electronic Prescription Service and electronic repeat dispensing.</td>
  </tr>
</table>

It is recommended that this structure is **omitted** for the target use case.

<hr/>

### extension (statusReason)

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Extension</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>CareConnect</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>To record the reason the medication (plan or order) was stopped and the date this occurred.</td>
  </tr>
</table>

**Note**: This extension was added to the international standard within FHIR R4.

<hr/>

### extension (prescriptionType)

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Extension</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>CareConnect</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>To record the type of prescription. An extension to support the NHS Electronic Prescription Service for the NHSBSA to identify the type of prescription.</td>
  </tr>
</table>

It is recommended that this structure is **omitted** for the target use case.
 
**Note**: The value set for this STU3 extension aligns with the legacy HL7v3 'PrescriptionTreatmentType' vocab; `acute`, `repeat`, `repeat dispensing` and `delayed prescribing`. If UK Core R4 is extended to support this type of data then the extension name should ideally not be called 'prescriptionType' as it confused with a different legacy HL7v3 vocab for 'prescriptionType' which serves a different purpose.

<hr/>

### statusReason

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>TBC</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>TBC</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>TBC</td>
  </tr>
</table>

Guidance TBC.

<hr/>

### doNotPerform

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>TBC</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>TBC</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>TBC</td>
  </tr>
</table>

Guidance TBC.

<hr/>

### reported[x]

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>TBC</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>TBC</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>TBC</td>
  </tr>
</table>

Guidance TBC.

<hr/>

### encounter

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>TBC</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>TBC</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>TBC</td>
  </tr>
</table>

Guidance TBC.

<hr/>

### performer

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>The specified desired performer of the medication treatment, e.g. the performer of the medication administration.</td>
  </tr>
</table>

**IMPORTANT**: Do not confuse this element with **dispenseRequest.performer** which defines any intended dispenser for the medication request using a referenced FHIR [Organization](develop_organization.html) resource.

<hr/>

### performerType

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>CodeableConcept</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Indicates the type of performer of the administration of the medication.</td>
  </tr>
</table>

Guidance TBC.

<hr/>

### instantiatesCanonical

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>TBC</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>TBC</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>TBC</td>
  </tr>
</table>

Guidance TBC.

<hr/>

### instantiatesUri

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>TBC</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>TBC</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>TBC</td>
  </tr>
</table>

Guidance TBC.

<hr/>

### courseOfTherapyType

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>TBC</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>TBC</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>TBC</td>
  </tr>
</table>

Guidance TBC.

<hr/>

### insurance

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>TBC</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>TBC</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>R4</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>TBC</td>
  </tr>
</table>

Guidance TBC.

<hr/>

### context

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..1</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>A link to an encounter or episode of care, that identifies the particular occurrence or set occurrences of contact between patient and health care provider.</td>
  </tr>
</table>

This element has been removed from the FHIR R4 standard.

If implemented as a reference to an **Encounter** resource, within R4 this is supported by a new **encounter** element, giving a clear migration path.

Any reference to an **EpisodeOfCare** resource within an STU3 or CareConnect implementation will not be supported if migrated to FHIR R4. It is recommended not to reference an EpisodeOfCare resource within an STU3 implementation.

<hr/>

### definition

<table class='resource-attributes'>
  <tr>
   <td><b>Data Type:</b></td>
   <td><code>Reference</code></td>
  </tr>
  <tr>
   <td><b>Required/Cardinality:</b></td>
   <td>Optional 0..*</td>
  </tr>
  <tr>
    <td><b>Version Support:</b> </td>
    <td><code>STU3</code></td>
  </tr>
  <tr>
   <td><b>Description:</b></td>
   <td>Protocol or definition followed by this request.</td>
  </tr>
</table>

This element has been removed from FHIR R4 therefore it's use within an STU3 or CareConnect implementation is **not recommended**.

<hr/>
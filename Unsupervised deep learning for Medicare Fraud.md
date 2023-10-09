[[Machine Learning]]

## Background
- Fraud, Waste & Abuse (FWA)  results in unnecessary treatments & medical payments to the estimated scale of 3% of all healthcare costs in the US ($100B and growing) 
###### Types of FWA
- **procedure (CPT/HCPCS) code overutilization**: one or more prescribed procedures may not be relevant to a given diagnosis/patient profile
	- largest component of FWA in the US system
	- between 20%-30% of total healthcare costs
- **kickbacks**: collusion between a patient and a provider to gain commission for services that are not rendered
- **Upcoding**: provider submits innaccurate and expensive billing codes which would result in inflated reimbursements
- 
### Challenges
- major challenges to identify FWA with ML include:
	- reliance on high-quality labeled data for supervised models
	- inability of static models to capture dynamic nature of fraudulent behaviors
- State medicare agencies have traditionally used rules-based & volume-based analysis using a Surveillance Utilization Review System (SURS) to identify and reduce potential overutilization
	- required by federal regulation
	- each state can design their own
	- analyzes multiple claims as part of identifying billing patterns that potentially indicate overutilization, which are then manually reviewed by regulators
	- lack of standardization leads to vast performance differences between states

### Approach
- unsupervised machine learning model trained on claims data containing the following features:
	- Claim Number: an identifier for each claim
	- Diagnosis Codes: represented by International Classification of Diseases (ICD-10) codes [19] that specify the codified medical diagnosis for a patient as submitted by a healthcare provider
	- Procedure Codes: represented by Current Procedural Terminology (CPT) or Healthcare Common Procedure Coding System (HCPCS) codes [12, 20, 21] that indicate the codified procedures performed for the given diagnosis, as submitted by a healthcare provider
	- Patient Demographics: age at the time of claim sub- mission (derived from the difference between date of submission of the claim and patient’s date of birth), and gender
	- Provider ID: an identifier for the healthcare provider in the form of a National Provider Identifier (NPI) [22]
	- Member ID: an identifier for the Medicare beneficiary/patient
	- Claim Start and End Dates: the dates the first and last procedures associated with a claim are performed
	- Billed Amount: how much the healthcare provider billed for the procedures.
- model learns specific combinations of procedure codes that are typically associated with the other features in the data
	- outlier procedure code is one that a model identifies as not belonging with the other combinations of procedures, diagnoses, and demographic information
	- for 
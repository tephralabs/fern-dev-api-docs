# Requirements for individuals

## Overview

Individual customers are required to provide certain information to complete identity verification. Fern oversees the entire verification process on your behalf, eliminating the need for you to manage it directly.

## Information collected

* Legal first name
* Middle name (optional)
* Legal last name
* Phone number
* Nationality
* Residential address
* Date of birth
* National ID issuing country
* National ID number
* Government ID issuing country
* Government ID number
* Government-issued identity document
* Employment status
* Most recent occupation (see [Occupation codes](occupation-codes.md))
* Source of funds
* Description of source of funds
* Account purpose
  * If `OTHER` is selected, free text description of the account purpose is required
* Expected monthly transaction volume (in USD)
* Proof of address
  * Mandatory for access to SEPA rails
  * API makes it possible to provide this optionally; if not provided, Customer will not have access to SEPA rails
  * Fern-hosted KYC forms require the proof of address

#### File Size Requirements

* **File size:** Greater than 10KB and less than 5MB
* **Total API request size:** Maximum 100MB
* **Image resolution:** Minimum 200x200 pixels

### Additional Information Requests (RFI)

If Fern's compliance team needs additional information during the verification process, the customer status will change to `NEEDS_ADDITIONAL_INFORMATION`, and the customer will be contacted directly via email.

**Common RFI scenarios for individuals:**

* Expired or unclear government ID
* Additional proof of address needed
* Verification of source of funds
* Enhanced due diligence for high-risk profiles

Customers can submit additional documents through the same `verificationLink` provided in the initial response.

**Typical time to approval:** <10min, unless we receive an RFI from partners (which requires manual intervention)

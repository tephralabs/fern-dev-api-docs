# Requirements for individuals

## Overview

Individual customers are required to provide certain information to complete identity verification. Fern oversees the entire verification process on your behalf, eliminating the need for you to manage it directly.

## Information collected

* First name
* Middle name (optional)
* Last name
* Phone number
* Physical address
* Date of birth
* Nationality
* National ID issuing country
* National ID number
* Government ID issuing country
* Government ID number
* Government-issued identity document
* Employment status
* Most recent occupation (see [Occupation codes](occupation-codes.md))
* Source of funds
* Account purpose
  * If `OTHER` is selected, free text description of the account purpose is required
* Expected monthly transaction volume (in USD)
* Proof of address
  * Mandatory for access to SEPA rails
  * API makes it possible to provide this optionally; if not provided, Customer will not have access to SEPA rails
  * Fern-hosted KYC forms require the proof of address

**Typical completion time:** <5 min&#x20;

**Typical time to approval:** <10min, unless we receive an RFI from partners (which requires manual intervention)

If additional documents or information are needed (e.g. if the uploaded ID has expired), the Fern compliance team will contact your end customer directly to complete the verification process.

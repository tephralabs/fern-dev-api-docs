# Requirements for businesses

## Overview

Business customers must complete verification of the nature of the business, business registration, and associated persons. Fern oversees the entire verification process on your behalf, eliminating the need for you to manage it directly.

## Information collected

* **Business details**
  * Legal business name
  * DBA name (if applicable)&#x20;
  * Business type
  * Business industry
  * Whether the business is a DAO
  * Business registered address
  * Business operating address
    * Can be different from registered address
  * Country of business registration
  * Business registration number
  * Business formation date
  * Website
  * Business description
  * Additional links
    * Social media profiles
* **Source of funds**&#x20;
  * Source of funds
    * In some cases, depending on the nature of the business, we require proof of source of funds
  * Description of source of funds
  * Compliance screening procedures
    * Required if transmitting funds on behalf of others
* **Account usage**
  * Expected monthly transaction volume in USD
  * Account purpose
    * If `OTHER` is selected, free text description of the account purpose is required
  * Estimated annual revenue
* **Risk information**
  * Operation in prohibited countries
  * High-risk activities & explanation of these activities
* **Beneficial owner information**
  * Refer to **Individual** requirements
  * Email address
  * Percentage ownership
  * Date relationship with the business began
* **Controller information**
  * Refer to **Individual** requirements
  * Email address
  * Title
  * Date relationship with the business began
* **Business** **documents**
  * Formation documents
  * Ownership documents
  * Business proof of physical address

**Typical time to approval:** 4-7 business days

If additional documents or information are needed (e.g., proof of business activities), our compliance team will contact your end-customer directly to complete the verification process.

### Specific document requirements

The Fern customer verification links will make it clear what information is required from the customer. Customers will be required to provide documents that meet these criteria:

<details>

<summary>Formation documents</summary>

#### The documents must:

* Include the entity name and business registration number
* Be filed with the secretary of state or similar government body
* Business name must match what was provided in the KYB form

#### Accepted documents include:&#x20;

* Official Articles of Incorporation / Organization
* Official Certificate of Formation
* Official Certificate or Registration of Limited Liability Partnership
* Legal Entity Formation (for DAOs)
* Trust Deed or Trust Agreement (for Trusts)

</details>

<details>

<summary>Ownership documents</summary>

#### The documents must:&#x20;

* Confirm all individual beneficial owners who own 25% or more of the underlying entity
* Clearly account for 100% of ownership
* Be officially verified:
  * Self-generated documents: Must be dated and signed by a lawyer or third-party CPA
  * Capitalization tables: No signature required if produced by well-known equity management platforms like Carta, AngelList, or Securitize
* If the entity has another entity as a benefiical owner, confirmation of the individuals with significant control over the entity, such as its directors or officers, will be required

#### Accepted documents include:

* Articles of Incorporation (listing shareholders, not just controller)
* Capitalization table
* Shareholder registry
* Operating agreement
* Membership schedule
* Stock purchase agreement
* Ownership attestation&#x20;
* Directors registry signed by a lawyer or CPA

</details>

<details>

<summary>Business proof of address</summary>

**The documents must:**

* Confirm the current operating address
* Mention the applying entity by name
* Be issued in the last 90 days
* Contain an address that is not a PO box or a virtual address

**Accepted documents include:**

* Bank statement

Note: DAOs may provide a proof of physical residential address for their verified control person if the DAO does not have its own physical operating address.

</details>

#### File Size Requirements

* **File size:** Greater than 10KB and less than 5MB
* **Total API request size:** Maximum 100MB
* **Image resolution:** Minimum 200x200 pixels

### Additional Information Requests (RFI)

If Fern's compliance team needs additional information during the verification process, the customer status will change to `NEEDS_ADDITIONAL_INFORMATION`, and the customer will be contacted directly via email.

**Common RFI scenarios for businesses:**

* Unclear formation or ownership documents
* Missing beneficial owner verification
* Proof of business activities required
* Enhanced due diligence documentation
* Updated financial information

Customers can submit additional documents through the same `verificationLink` provided in the initial response.

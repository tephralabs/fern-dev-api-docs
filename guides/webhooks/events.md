# Events

Fern’s webhooks are organized into several event categories based on the type of resource or process. Each event has a specific event type string (for example, customer.created) that identifies the nature of the event. The available event types and their meanings are listed below:\


### Event Types (by Categories):

| **Event Category**     | **Event Type**           | **Description**                                                                                                                                                                               |
| ---------------------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Customer Events        |                          | —                                                                                                                                                                                             |
|                        | customer.created         | Triggered when a new customer is initially created.                                                                                                                                           |
|                        | customer.updated         | Triggered when a customer’s status or details change. _(Status values can include  PENDING, REJECTED, ACTIVE, DEACTIVATED.)_                                                                  |
| Payment Account Events |                          | —                                                                                                                                                                                             |
|                        | payment\_account.created | Triggered when a new payment account is added/created.                                                                                                                                        |
|                        | payment\_account.deleted | Triggered when a payment account is removed/deleted.                                                                                                                                          |
| Transaction Events     |                          | —                                                                                                                                                                                             |
|                        | transaction.created      | Triggered when a transaction is initiated (created).                                                                                                                                          |
|                        | transaction.updated      | Triggered when a transaction’s status changes (e.g. moves from created to processing, completed, etc). _(Status values include AWAITING\_TRANSFER, PROCESSING, COMPLETED, REFUNDED, FAILED.)_ |

### Event Structure:

All webhook event payloads share a common structure. The HTTP POST request body is a JSON object with the following top-level fields:

* **id (string)**: A unique identifier for the event notification. This can be used to detect duplicate deliveries.
* **apiVersion (string)**: The API version for the payload format (e.g. "v1").
* **type (string)**: The event type name (as listed above, e.g. "customer.created").
* **createdAt (string)**: Timestamp (ISO 8601 format) when the event occurred.
* **resource (object)**: The payload data specific to the event, containing the resource or entity information. The content of this object varies by event type:
  * For customer events, the resource includes details about the customer (e.g. customerId, customerStatus, name, email, etc.), which matches the response of the `GET` Customer end-point response.
  * For payment account events, the resource includes details of the payment account, which matches the `GET` Payment-Accounts end-point response
  * For transaction events, the resource includes details of the transaction, which matches the `GET` Transaction end-point response

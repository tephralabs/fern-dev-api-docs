# Webhooks

Webhooks allow your application to receive real-time notifications from Fern whenever certain events occur (e.g. a new customer is created or a transaction status changes) instead of having to continuously poll the API. When an event is triggered, Fern will send an HTTP POST request to the webhook endpoint URL that you have configured. The request body will contain a JSON payload describing the event, and it will include headers to help you verify the authenticity of the request.

To start receiving webhooks, you need to register a webhook subscription (via the Fern dashboard or API) and provide an endpoint URL (must be https:// and publicly accessible). Each subscription is associated with a unique secret that Fern uses to sign the requests (this secret is shown only once at creation, so store it securely). Your endpoint should respond with a 200 OK status as quickly as possible upon receiving a webhook. If Fern does not receive a 2xx response, it may retry delivering the event (with backoff) to ensure reliable delivery. Always implement your webhook handler to be idempotent, as the same event could be sent multiple times (e.g., after retries).

Key benefits of using webhooks:

* **Real-time updates**: Your application gets notified immediately when important events happen (no need for polling).
* **Event-driven logic**: You can trigger business workflows in response to events (e.g. update your database when a transaction completes).
* **Reliable delivery**: Fern will attempt to deliver events even if your service is temporarily unavailable, ensuring you eventually receive the notification.

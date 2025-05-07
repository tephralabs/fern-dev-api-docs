# Retries

Fern automatically retries webhook deliveries if your server does not respond with a 2xx HTTP status code.

### Retry Policy

Each webhook event will be attempted up to 4 times total (1 initial + 3 retries) using an exponential backoff strategy:

| Attempt | Delay Before Retry   |
| ------- | -------------------- |
| 1st     | Immediate (no delay) |
| 2nd     | 5 seconds            |
| 3rd     | 30 seconds           |
| 4th     | 1 minute             |

If all attempts fail (i.e., your endpoint continues to return a non-2xx response or times out), the webhook will be marked as undelivered and no further attempts will be made.\


> We recommend that your webhook handlers are idempotent and respond quickly (within 5 seconds). To ensure reliability, log incoming webhook ids (from the payload `id` ) and avoid side effects on duplicate deliveries.

# table_03_the-incident-revealed-multiple-failures-b1d7


| Failure Point | What Happened | Root Cause |
|---------------|---------------|------------|
| **Rate Limiting** | Malicious actors sent 10,000 requests/second | No rate limiting configured |
| **Idempotency** | Client retries created duplicate orders | No idempotency keys implemented |
| **Payment Processing** | Payment gateway charged each retry | API forwarded every request to payment service |
| **Customer Support** | 500+ duplicate charge complaints | System couldn't detect duplicates |
| **Refund Processing** | $10,000+ in refunds | Each duplicate required manual refund |

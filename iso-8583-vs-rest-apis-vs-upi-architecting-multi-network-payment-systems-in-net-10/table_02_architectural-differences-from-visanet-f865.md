# table_02_architectural-differences-from-visanet-f865


| Aspect | VisaNet/Mastercard | UPI |
|--------|-------------------|-----|
| **Protocol** | ISO 8583 (binary, bitmapped) | REST APIs with JSON (over HTTPS) |
| **Network** | Private, dedicated circuits with leased lines | Public internet with TLS 1.3 encryption |
| **Primary Identifier** | Card number (PAN) 16 digits | Virtual Payment Address (VPA) like "user@bank" |
| **Authentication** | PIN, signature, 3DS 2.0, biometric | MPIN (4-6 digits), device binding, biometric |
| **Settlement** | Batch settlement (T+1/T+2) | Near real-time (continuous settlement, T+0) |
| **Transaction Flow** | Acquirer → Scheme → Issuer | PSP → NPCI → Banks (debit/credit simultaneously) |
| **Interoperability** | Card scheme governed with scheme rules | Bank-to-bank directly via NPCI with standardized APIs |
| **Developer Onboarding** | Weeks to months (certification required) | Days (self-service portal with sandbox) |
| **Cost Structure** | Interchange fees + scheme fees + assessment fees | Zero MDR (Merchant Discount Rate) for P2P, capped for P2M |
| **Availability** | 99.99% SLA on private network | 99.95% SLA on public cloud infrastructure |
| **State Management** | Message-based (ISO MTI states: 0100→0110) | API-based with callback URLs and webhooks |
| **HSM Requirement** | Mandatory at acquirer and issuer | Optional at PSP level, managed by NPCI |
| **Time to Market (New Feature)** | 12-18 months (ISO governance) | 2-4 weeks (API versioning) |

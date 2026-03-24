# table_03_comparison-matrix


| Aspect | VisaNet | Mastercard Network | RuPay | UPI |
|--------|---------|-------------------|-------|-----|
| **Established** | 1973 | 1966 | 2012 | 2016 |
| **Protocol** | ISO 8583 (binary) | ISO 8583 (binary) | ISO 8583 (binary) | REST/JSON over HTTP |
| **Network** | Private dedicated circuits | Private dedicated circuits | Private circuits | Public internet with TLS 1.3 |
| **Primary Identifier** | PAN (16 digits) | PAN (16 digits) | PAN (16 digits) | VPA (user@bank) |
| **Authentication** | PIN, 3DS 2.0, signature | PIN, 3DS 2.0, signature | PIN, 3DS | MPIN, biometric, device binding |
| **Settlement** | T+1/T+2 batch | T+1/T+2 batch | T+1 batch | Near real-time (continuous, T+0) |
| **Transaction Flow** | Acquirer → Scheme → Issuer | Acquirer → Scheme → Issuer | Acquirer → RuPay → Issuer | PSP → NPCI → Banks (dual debit/credit) |
| **Interoperability** | Card scheme governed | Card scheme governed | Domestic scheme | Bank-to-bank via NPCI |
| **Developer Onboarding** | Weeks to months (certification) | Weeks to months (certification) | Weeks | Days (self-service portal) |
| **Cost Structure** | Interchange + scheme fees | Interchange + scheme fees | Lower interchange | Zero MDR for P2P, capped for P2M |
| **Availability** | 99.99% SLA | 99.99% SLA | 99.98% | 99.95% |
| **State Management** | Message-based (ISO MTI) | Message-based (ISO MTI) | Message-based | API callbacks + webhooks |
| **HSM Requirement** | Mandatory | Mandatory | Mandatory | Optional (PSP managed) |
| **Geographic Reach** | 200+ countries | 210+ countries | India (expanding) | India (expanding internationally) |
| **Time to Market** | 12-18 months | 12-18 months | 6-12 months | 2-4 weeks |
| **Real-time Settlement** | No | No | No | Yes (T+0) |
| **Open Banking API** | Visa Direct (API) | Mastercard Send (API) | RuPay API | Native REST/JSON |

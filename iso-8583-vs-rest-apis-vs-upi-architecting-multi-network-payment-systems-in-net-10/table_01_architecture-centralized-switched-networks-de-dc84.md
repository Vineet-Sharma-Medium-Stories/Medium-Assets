# table_01_architecture-centralized-switched-networks-de-dc84


| Aspect | VisaNet | Mastercard Network |
|--------|---------|-------------------|
| **Established** | 1973 | 1966 |
| **Protocol** | ISO 8583 with Visa-specific DE 48 extensions | ISO 8583 with Mastercard-specific DE 48/DE 63 |
| **Infrastructure** | Private, dedicated circuits with 99.999% uptime | Private, dedicated circuits with 99.999% uptime |
| **Message Routing** | BIN-based (first 6 digits of PAN) with global switching centers | BIN-based with regional processing hubs |
| **State Management** | Built-in transaction lifecycle (authorization → clearing → settlement) | Built-in transaction lifecycle with additional fraud controls |
| **Idempotency** | STAN (System Trace Audit Number) + RRN (Retrieval Reference Number) ensures duplicate detection across network | STAN + RRN with network-level duplicate checking |
| **Security** | Hardware Security Modules (HSMs) at every node, Triple DES/AES encryption, MAC (Message Authentication Code) | HSMs, DUKPT key management, CVV2/CVC2 validation |
| **Settlement** | T+1 (next day) batch settlement | T+1 batch settlement with net settlement positions |
| **Geographic Reach** | 200+ countries, 100M+ merchant locations | 210+ countries, 90M+ merchant locations |

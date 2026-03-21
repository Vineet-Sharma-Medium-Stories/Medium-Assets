# table_12_real-world-consequences


| Violation | Impact | Real Example from the Incident |
|-----------|--------|--------------------------------|
| **Password hash exposed** | Security breach | Customer passwords in API responses |
| **Credit card data exposed** | PCI violation | Full card numbers in logs |
| **Circular references** | JSON serialization errors | API crashes on certain queries |
| **Tight coupling** | Database changes break API | New fields appear without consent |

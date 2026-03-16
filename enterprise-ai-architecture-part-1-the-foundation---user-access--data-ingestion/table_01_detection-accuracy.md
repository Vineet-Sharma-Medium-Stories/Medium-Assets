# table_01_detection-accuracy


| PII Type | Detection Method | Accuracy | Action |
|----------|-----------------|----------|--------|
| SSN | Pattern + Checksum | 99.5% | Block |
| Credit Card | Luhn Algorithm + Pattern | 99.8% | Block |
| Email | Regex | 99.9% | Mask |
| Phone | Regex + Context | 98.5% | Mask |
| Person Name | NER Model | 94% | Replace |
| Address | NER + Regex | 87% | Generalize |

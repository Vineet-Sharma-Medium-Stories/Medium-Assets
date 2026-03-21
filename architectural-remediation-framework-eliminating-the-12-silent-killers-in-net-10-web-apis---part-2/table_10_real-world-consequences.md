# table_10_real-world-consequences


| Violation | Impact | Real Example from the Incident |
|-----------|--------|--------------------------------|
| **Database I/O** | 10x more data read from disk | 95% CPU, slow queries |
| **Network Transfer** | 10x more data over network | API timeouts on slow connections |
| **Memory Allocation** | 10x more memory in API | 8GB memory usage, GC pressure |
| **Serialization** | 10x more JSON to serialize | Response time dominated by serialization |

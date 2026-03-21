# table_16_81-part-2-accomplishments


| Anti-Pattern | Solution Implemented | Key Outcome |
|--------------|---------------------|-------------|
| **#6 No Pagination** | Skip/Take with metadata headers | Data transferred reduced by 99.5% |
| **#7 Wrong Status Codes** | Proper HTTP semantics with Problem Details | Clients can distinguish errors |
| **#8 Over-fetching Data** | Projections with .Select() | Query time reduced by 98% |
| **#9 Returning EF Entities** | DTOs with AutoMapper | Complete decoupling from database |

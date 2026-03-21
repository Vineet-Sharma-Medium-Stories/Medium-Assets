# table_06_real-world-consequences


| Consequence | Impact | Real Example from the Incident |
|-------------|--------|--------------------------------|
| **Untestable** | 0% unit test coverage | Mocking HTTP context, database, and external services simultaneously was impossible |
| **Merge Conflicts** | 3-4 hours per merge | Two developers modified the same 3,200-line file, losing a day to merge resolution |
| **Bug Propagation** | Regression in 40% of changes | Changing the discount logic broke payment processing |
| **Cognitive Load** | 2-3x development time | Understanding 3,200 lines to fix a simple bug took 4 hours |

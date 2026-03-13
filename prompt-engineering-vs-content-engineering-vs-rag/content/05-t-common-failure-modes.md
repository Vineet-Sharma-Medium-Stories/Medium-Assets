# Table 5: Common Failure Modes:

| Problem | Manifestation | Root Cause |
|---------|---------------|------------|
| **Duplicate documents** | RAG returns three slightly different versions of same policy | No canonical source identification |
| **Outdated policies** | AI cites 2021 regulation, 2024 amendment exists | No version tracking |
| **Inconsistent formats** | Some documents chunk well, others fragment | No standardized ingestion |
| **Poor chunking** | Retrieved text cuts off mid-sentence, mid-thought | Naive character splitting |
| **Missing metadata** | Indian employee receives US benefits information | No region tags |
| **Scanned PDFs** | OCR garbage → embedding garbage → retrieval garbage | No OCR quality validation |
| **Conflicting terminology** | "Parental leave" in one doc, "Family leave" in another | No ontology alignment |

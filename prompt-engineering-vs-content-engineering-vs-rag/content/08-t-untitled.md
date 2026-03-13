# Table 8: Untitled

| Aspect | Prompt Engineering | Content Engineering | RAG |
|--------|-------------------|---------------------|-----|
| **Core Question** | How should the model behave? | What knowledge exists? | What knowledge is used now? |
| **Primary Artifact** | Prompt templates, system messages | Chunks, embeddings, metadata | Retrieval pipeline, augmented prompts |
| **Source of Truth** | Model's training + instructions | Enterprise documents | Retrieved context at query time |
| **Update Frequency** | Per prompt version | Continuous ingestion | Per query |
| **Failure Mode** | Inconsistent behavior, refusal | Garbage in, garbage out | Missing context, irrelevant retrieval |
| **Cost Driver** | Input + output tokens | Embedding generation + storage | Retrieval + augmented generation |
| **Observability** | Response quality, token usage | Document coverage, chunk quality | Retrieval relevance, citation accuracy |
| **.NET Implementation** | TemplateService, LLMClient | BackgroundService, VectorStore | RAGOrchestrator, MetadataFilter |
| **Dependency** | Model capability | Document quality | Vector search quality |
| **Maturity Level** | Demo → MVP | MVP → System | System → Platform |

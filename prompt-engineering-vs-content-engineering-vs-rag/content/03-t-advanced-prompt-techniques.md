# Table 3: ⚙️ Advanced Prompt Techniques

| Technique | Purpose | Implementation |
|-----------|---------|----------------|
| **Response length constraints** | Cost control, conciseness | `MaxTokens: 100`, "Respond in exactly 3 sentences" |
| **Refusal enforcement** | Safety, compliance | "If the request violates policy, respond ONLY with: 'I cannot assist with this request.'" |
| **Citation requirements** | Traceability, verification | "For each statement, cite the policy section in brackets [Section X.Y]" |
| **Context usage enforcement** | Hallucination prevention | "If the answer is not found in the context provided, respond: 'I do not have sufficient information to answer this question.'" |
| **Tool invocation** | Agent capabilities | "When you need to check real-time data, respond with: TOOL_CALL: get_weather[city_name]" |
| **Multi-language constraints** | Global deployment | "Respond in the same language as the user's question" |

# table_12_benefits-summary


| Metric | Sync-over-Async (.Result) | True Async (await) | Improvement |
|--------|---------------------------|-------------------|-------------|
| **Threads Used per Request** | 1 (blocked) | ~0.1 (only during CPU work) | **90%** reduction |
| **Max Concurrent Requests (4 CPU cores)** | ~40 (thread pool limit) | ~10,000 (I/O bound) | **250x** increase |
| **Memory per Request** | 1MB+ (thread stack) | <100KB | **90%** reduction |
| **Response Time under Load** | Exponential degradation | Linear scaling | **95%** improvement |
| **Cancellation Support** | None | Full support | **100%** coverage |

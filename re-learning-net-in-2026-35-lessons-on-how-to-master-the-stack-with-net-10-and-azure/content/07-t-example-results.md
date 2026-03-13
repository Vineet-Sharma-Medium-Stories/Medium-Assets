# Table 7: Example Results:

| Method        | Count | Mean        | Allocated | Gen0   | Rank |
|--------------|------|------------|----------|--------|------|
| PlusOperator | 10   |   1,234 ns |   1.2 KB | 0.1000 | 4    |
| StringBuilder| 10   |     856 ns |   0.9 KB | 0.0500 | 3    |
| StringConcat | 10   |     423 ns |   0.5 KB | 0.0100 | 1    |
| StringJoin   | 10   |     512 ns |   0.6 KB | 0.0100 | 2    |
|--------------|------|------------|----------|--------|------|
| PlusOperator | 100  | 125,432 ns | 125.4 KB | 10.000 | 4    |
| StringBuilder| 100  |   3,456 ns |   3.8 KB | 0.2000 | 3    |
| StringConcat | 100  |   1,234 ns |   1.2 KB | 0.1000 | 1    |
| StringJoin   | 100  |   1,567 ns |   1.5 KB | 0.1000 | 2    |

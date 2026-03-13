# Table 5: How to Read It:

| Line | What It Tells You |
|------|-------------------|
| Exception type | `NullReferenceException` - something was null |
| First line (top) | Exactly where it happened - `OrderService.cs:line 42` |
| Second line | Who called it - `OrderController.PlaceOrder` |
| Bottom lines | Framework code - ignore unless relevant |
| Middle lines | Middleware pipeline - shows request flow |

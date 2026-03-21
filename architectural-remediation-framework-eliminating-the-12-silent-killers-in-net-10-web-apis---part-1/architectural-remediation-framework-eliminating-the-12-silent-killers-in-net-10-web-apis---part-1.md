
# Architectural Remediation Framework: Eliminating the 12 Silent Killers in .NET 10 Web APIs - Part 1

## Foundations: Observability, Async Patterns, and Exception Handling

![alt text](<images/Architectural Remediation Framework: Eliminating the 12 Silent Killers in .NET 10 Web APIs - Part 1.png>)
## Introduction

The e-commerce platform had been operating in production for approximately 18 months and was consistently serving thousands of customers on a daily basis. The engineering team had focused heavily on rapid feature delivery and continuous releases. However, during a high-traffic holiday promotion, the system encountered a critical failure.

Within minutes of the promotion launch, the checkout API began to degrade under load. The platform exhibited multiple severe symptoms: duplicated orders, repeated payment charges, and a growing rate of HTTP 500 responses. Transaction integrity and customer trust were immediately impacted.

Operational dashboards indicated systemic resource exhaustion. Application thread pools were fully consumed, database connection pools were saturated, and unhandled exceptions were being returned directly to clients. Observability was insufficient—there were no distributed traces, structured logs, or correlated diagnostics to support rapid root-cause analysis.

A subsequent post-incident review identified the underlying cause as the gradual accumulation of architectural debt. Over time, several design and implementation anti-patterns had been introduced incrementally. Each change appeared benign in isolation, but together they created a fragile system that was unable to withstand peak traffic conditions. The absence of proactive architectural governance allowed these issues to remain undetected until they manifested as a production outage.

This three-part architectural framework documents the remediation strategy implemented to stabilize and modernize the platform. The approach has since been successfully applied across multiple enterprise systems operating at high scale, including environments processing tens of thousands of requests per second. Each part of the framework addresses a specific group of anti-patterns and presents detailed technical solutions built on the latest capabilities of the .NET platform.

### The Twelve Silent Killers

![### The Twelve Silent Killers](images/table_01_the-twelve-silent-killers.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_01_the-twelve-silent-killers.md)


### Why This Matters

In my 12 years of experience reviewing production codebases across fintech, healthcare, and e-commerce, these patterns appear consistently. They are "silent killers" because:

- **They accumulate gradually**—individual violations seem innocuous during development
- **They manifest only under stress**—production traffic reveals scaling limitations
- **They obscure root causes**—poor observability makes debugging a forensic exercise
- **They create coupling**—changes in one area unpredictably break seemingly unrelated features

### Part 1 Overview

This first part establishes the foundation upon which all other improvements depend. Without proper observability, async patterns, and exception handling, no amount of optimization will prevent production failures. We'll cover:

1. **Fat Controllers** → Moving business logic to MediatR handlers
2. **No Input Validation** → Implementing FluentValidation with declarative rules
3. **Raw Exceptions** → Global exception handling with RFC 7807 Problem Details
4. **Blocking Async** → True async/await patterns with cancellation token propagation
5. **Ignoring CancellationTokens** → Resource-efficient request handling
6. **No Observability** → OpenTelemetry, Serilog, and distributed tracing

---

## Table of Contents - Part 1

1. [Executive Summary - Part 1](#1-executive-summary---part-1)
2. [Current State Analysis](#2-current-state-analysis)
3. [Architectural Principles](#3-architectural-principles)
4. [Anti-Pattern Deep Dives](#4-anti-pattern-deep-dives)
   - [4.1 Anti-Pattern 1: Fat Controllers](#41-anti-pattern-1-fat-controllers)
   - [4.2 Anti-Patterns 2-3: Validation & Exception Handling](#42-anti-patterns-2-3-validation--exception-handling)
   - [4.3 Anti-Patterns 4-5: Async & Cancellation](#43-anti-patterns-4-5-async--cancellation)
   - [4.4 Anti-Pattern 11: Observability](#44-anti-pattern-11-observability)
5. [Implementation Guide - Part 1](#5-implementation-guide---part-1)
6. [Monitoring & Observability](#6-monitoring--observability)
7. [Migration Strategy - Part 1](#7-migration-strategy---part-1)
8. [Summary & Next Steps](#8-summary--next-steps)

---

## 1. Executive Summary - Part 1

### 1.1 The Foundation Problem

Most .NET applications fail not because of complex business logic errors, but because fundamental architectural principles were violated during development. The six anti-patterns addressed in this part represent the foundation of any production-ready API:

![Most .NET applications fail not because of complex business logic errors, but because fundamental architectural principles were violated during development. The six anti-patterns addressed in this part represent the foundation of any production-ready API:](images/table_02_most-net-applications-fail-not-because-of-complex-685a.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_02_most-net-applications-fail-not-because-of-complex-685a.md)


### 1.2 The Story: What Went Wrong

Lets discuss about the e-commerce platform that brought us in at 3:47 AM. The codebase had been built by a talented team that prioritized feature delivery over architectural discipline. Here's what we found when opened the code:

```csharp
// The horror I discovered in CheckoutController.cs - 3,200 lines
[ApiController]
[Route("api/[controller]")]
public class CheckoutController : ControllerBase
{
    private readonly AppDbContext _context;
    
    public CheckoutController(AppDbContext context)
    {
        _context = context;
    }
    
    [HttpPost]
    public IActionResult CreateOrder([FromBody] object request) // Raw object, no validation
    {
        // 1. Manual validation (200 lines)
        var json = request.ToString();
        if (string.IsNullOrEmpty(json)) return Ok(new { error = "Invalid" });
        
        // 2. Business logic (500 lines)
        var userId = GetUserIdFromJson(json);
        var user = _context.Users.Find(userId); // Synchronous! Blocks thread
        
        // 3. Data access (300 lines)
        var cart = _context.Carts.Include(c => c.Items)
            .First(c => c.UserId == user.Id); // No cancellation token
        
        // 4. More logic (800 lines)
        var order = new Order { /* mapping */ };
        _context.Orders.Add(order);
        _context.SaveChanges(); // Synchronous!
        
        // 5. External calls (200 lines)
        _emailService.SendConfirmation(user.Email); // No async, no cancellation
        
        // 6. Return raw entity (exposed to client)
        return Ok(order); // 200 OK for everything, even errors
    }
}
```

This single method was:
- **3,200 lines** across the entire controller
- Using **sync-over-async** with `.Find()` and `.SaveChanges()`
- **No validation** beyond null checks
- **Returning raw EF entities** to clients
- **No cancellation tokens** anywhere
- **Returning 200 OK** for all responses
- **Zero structured logging** or tracing

The team had no idea these were problems. Each pattern had been introduced gradually, and the system had worked—until the holiday traffic revealed the accumulated debt.

### 1.3 The Rescue Plan

Over the next four weeks, we systematically eliminated these anti-patterns. This document captures that journey.

### 1.4 Success Metrics

![### 1.4 Success Metrics](images/table_03_14-success-metrics.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_03_14-success-metrics.md)


---

## 2. Current State Analysis

### 2.1 Anti-Pattern Severity Matrix - Part 1 Focus

```mermaid
```

![### 2.1 Anti-Pattern Severity Matrix - Part 1 Focus](images/diagram_01_21-anti-pattern-severity-matrix---part-1-focu-a36b.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/diagram_01_21-anti-pattern-severity-matrix---part-1-focu-a36b.md)


### 2.2 Root Cause Analysis - Part 1 Patterns

```mermaid
```

![### 2.2 Root Cause Analysis - Part 1 Patterns](images/diagram_02_22-root-cause-analysis---part-1-patterns-f911.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/diagram_02_22-root-cause-analysis---part-1-patterns-f911.md)


### 2.3 Technical Debt Assessment - Foundation Layer

![### 2.3 Technical Debt Assessment - Foundation Layer](images/table_04_23-technical-debt-assessment---foundation-lay-83dd.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_04_23-technical-debt-assessment---foundation-lay-83dd.md)


---

## 3. Architectural Principles

### 3.1 Core Principles for Part 1

```mermaid
```

![### 3.1 Core Principles for Part 1](images/diagram_03_31-core-principles-for-part-1.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/diagram_03_31-core-principles-for-part-1.md)


### 3.2 The Architectural Shift

Before remediation, the architecture looked like this:

```mermaid
```

![Before remediation, the architecture looked like this:](images/diagram_04_before-remediation-the-architecture-looked-like-t-f2ed.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/diagram_04_before-remediation-the-architecture-looked-like-t-f2ed.md)


After remediation, the architecture becomes:

```mermaid
```

![After remediation, the architecture becomes:](images/diagram_05_after-remediation-the-architecture-becomes-eb9a.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/diagram_05_after-remediation-the-architecture-becomes-eb9a.md)


### 3.3 Technology Stack - Part 1 Focus

![### 3.3 Technology Stack - Part 1 Focus](images/table_05_33-technology-stack---part-1-focus-094d.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_05_33-technology-stack---part-1-focus-094d.md)


---

## 4. Anti-Pattern Deep Dives

### 4.1 Anti-Pattern 1: Fat Controllers

#### Problem Analysis

**Definition**: Controllers that exceed 500+ lines of code (often 2,000-3,000 lines) containing validation logic, business rules, data access, external service calls, and HTTP concerns in a single class.

**The Story**: When I opened `CheckoutController.cs`, it was 3,200 lines. The team had been adding features for two years, and every developer was afraid to touch it. A simple change to the discount calculation broke order creation because of unexpected side effects. Merge conflicts were a daily occurrence.

**Real-World Consequences**:

![**Real-World Consequences**:](images/table_06_real-world-consequences.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_06_real-world-consequences.md)


**Code Smells Identification**:

```csharp
// ANTI-PATTERN INDICATORS I Found:
// 1. Class name "CheckoutController" but contained business logic for pricing, inventory, payments
// 2. Multiple [HttpPost] methods with 500+ lines each
// 3. Direct instantiation of services (new OrderService())
// 4. Using HttpContext directly for business decisions
// 5. SQL queries in controller actions via _context.Orders.ToList()
// 6. try/catch blocks handling domain exceptions
// 7. Static methods for helper functions
// 8. 15+ dependencies injected, all used in complex ways
```

#### Architectural Solution

**Design Pattern**: Mediator Pattern with CQRS (Command Query Responsibility Segregation)

**Component Responsibilities**:

![**Component Responsibilities**:](images/table_07_component-responsibilities.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_07_component-responsibilities.md)


**Complete Implementation**:

```csharp
namespace ECommerce.API.Controllers;

/// <summary>
/// Thin controller demonstrating Single Responsibility Principle.
/// Only handles HTTP concerns: routing, model binding, response formatting.
/// Business logic fully delegated to MediatR handlers.
/// </summary>
/// <remarks>
/// .NET 10 Advantages:
/// - Primary constructors reduce boilerplate
/// - Built-in problem details for error responses
/// - Native cancellation token support
/// </remarks>
[ApiController]
[Route("api/v{version:apiVersion}/[controller]")]
[Authorize(Policy = "VerifiedCustomer")]
[ApiVersion("1.0")]
public class OrdersController : ControllerBase
{
    private readonly IMediator _mediator;
    private readonly ILogger<OrdersController> _logger;

    // .NET 10: Primary constructor reduces boilerplate
    public OrdersController(
        IMediator mediator,
        ILogger<OrdersController> logger)
    {
        _mediator = mediator ?? throw new ArgumentNullException(nameof(mediator));
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));
        
        // Advantage: Dependency injection enables testability through mocking
        // .NET 10's primary constructors further simplify this pattern
    }

    /// <summary>
    /// Creates a new order with idempotency support.
    /// </summary>
    /// <param name="command">The order creation command</param>
    /// <param name="cancellationToken">Cancellation token for request abort</param>
    /// <returns>The created order or validation errors</returns>
    /// <response code="201">Order created successfully</response>
    /// <response code="400">Invalid request or validation failure</response>
    [HttpPost]
    [ProducesResponseType(typeof(OrderResponse), StatusCodes.Status201Created)]
    [ProducesResponseType(typeof(ValidationProblemDetails), StatusCodes.Status400BadRequest)]
    public async Task<IActionResult> CreateOrder(
        [FromBody] CreateOrderCommand command,
        CancellationToken cancellationToken)
    {
        // Controller is now a thin HTTP adapter - 5 lines vs 3,200+ lines previously
        _logger.LogInformation("Received order creation request for customer {CustomerId}", 
            command.CustomerId);
        
        var result = await _mediator.Send(command, cancellationToken);
        
        return result.Match<IActionResult>(
            order => CreatedAtAction(
                nameof(GetOrder), 
                new { id = order.Id, version = HttpContext.GetRequestedApiVersion()?.ToString() }, 
                order),
            problem => problem.ToActionResult()
        );
    }
    
    /// <summary>
    /// Retrieves an order by ID with proper cancellation support.
    /// Demonstrates projection to DTO and efficient querying.
    /// </summary>
    [HttpGet("{id:guid}")]
    [ProducesResponseType(typeof(OrderResponse), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<IActionResult> GetOrder(
        Guid id,
        CancellationToken cancellationToken)
    {
        // .NET 10 advantage: CancellationToken automatically bound from HTTP context
        var query = new GetOrderQuery(id);
        var result = await _mediator.Send(query, cancellationToken);
        
        return result.Match<IActionResult>(
            order => Ok(order),
            notFound => NotFound()
        );
    }
    
    /// <summary>
    /// Retrieves paginated orders for the authenticated customer.
    /// Demonstrates proper pagination with filtering and sorting.
    /// </summary>
    [HttpGet]
    [ProducesResponseType(typeof(PagedResult<OrderSummaryDto>), StatusCodes.Status200OK)]
    public async Task<IActionResult> GetOrders(
        [FromQuery] OrderFilter filter,
        CancellationToken cancellationToken)
    {
        // Filter is automatically bound from query string
        var query = new GetOrdersQuery
        {
            CustomerId = User.GetUserId(), // Extension method
            Page = filter.Page ?? 1,
            PageSize = Math.Min(filter.PageSize ?? 20, 100), // Cap at 100
            SortBy = filter.SortBy ?? "OrderDate",
            SortDescending = filter.SortDescending ?? true,
            Status = filter.Status,
            FromDate = filter.FromDate,
            ToDate = filter.ToDate
        };
        
        var result = await _mediator.Send(query, cancellationToken);
        
        // Add pagination headers for REST compliance
        Response.Headers.Add("X-Total-Count", result.TotalCount.ToString());
        Response.Headers.Add("X-Total-Pages", result.TotalPages.ToString());
        
        return Ok(result);
    }
}

// Command DTO with validation attributes
public record CreateOrderCommand : IRequest<ErrorOr<OrderResponse>>
{
    [Required]
    public Guid CustomerId { get; init; }
    
    [MinLength(1)]
    public List<OrderItemDto> Items { get; init; } = new();
    
    [Required]
    public ShippingAddressDto ShippingAddress { get; init; }
    
    [Required]
    public PaymentMethodDto PaymentMethod { get; init; }
    
    public string? CouponCode { get; init; }
    
    public string? Notes { get; init; }
}

// Command handler with business logic
public class CreateOrderHandler : IRequestHandler<CreateOrderCommand, ErrorOr<OrderResponse>>
{
    private readonly IOrderRepository _orderRepository;
    private readonly ICustomerRepository _customerRepository;
    private readonly IInventoryService _inventoryService;
    private readonly IPaymentService _paymentService;
    private readonly ILogger<CreateOrderHandler> _logger;
    private readonly OrderMetrics _metrics;

    public CreateOrderHandler(
        IOrderRepository orderRepository,
        ICustomerRepository customerRepository,
        IInventoryService inventoryService,
        IPaymentService paymentService,
        ILogger<CreateOrderHandler> logger,
        OrderMetrics metrics)
    {
        _orderRepository = orderRepository;
        _customerRepository = customerRepository;
        _inventoryService = inventoryService;
        _paymentService = paymentService;
        _logger = logger;
        _metrics = metrics;
    }

    public async Task<ErrorOr<OrderResponse>> Handle(
        CreateOrderCommand request,
        CancellationToken cancellationToken)
    {
        using var activity = DiagnosticsConfig.ActivitySource.StartActivity("CreateOrder");
        activity?.SetTag("customer.id", request.CustomerId);
        activity?.SetTag("order.item_count", request.Items.Count);
        
        using var timer = _metrics.MeasureOrderProcessingTime();
        
        try
        {
            // Validate customer exists and is active
            var customer = await _customerRepository.GetByIdAsync(request.CustomerId, cancellationToken);
            if (customer == null)
                return Error.NotFound("Customer not found");
            
            if (!customer.IsActive)
                return Error.Validation("Customer account is inactive");
            
            // Check inventory availability
            var inventoryCheck = await _inventoryService.CheckAvailabilityAsync(
                request.Items.Select(i => (i.ProductId, i.Quantity)),
                cancellationToken);
            
            if (!inventoryCheck.AllAvailable)
                return Error.Validation("Some items are out of stock");
            
            // Calculate totals with discounts
            var subtotal = request.Items.Sum(i => i.UnitPrice * i.Quantity);
            var discount = await ApplyDiscounts(request.CouponCode, subtotal, customer);
            var total = subtotal - discount;
            
            // Create domain entity
            var order = Order.Create(
                customer.Id,
                request.Items.Select(i => new OrderItem(i.ProductId, i.Quantity, i.UnitPrice)),
                request.ShippingAddress,
                total,
                discount);
            
            // Process payment
            var paymentResult = await _paymentService.ProcessPaymentAsync(
                order.Id,
                request.PaymentMethod,
                total,
                cancellationToken);
            
            if (!paymentResult.Success)
                return Error.Failure($"Payment failed: {paymentResult.ErrorMessage}");
            
            // Reserve inventory
            await _inventoryService.ReserveInventoryAsync(
                request.Items.Select(i => (i.ProductId, i.Quantity)),
                cancellationToken);
            
            // Persist order
            await _orderRepository.AddAsync(order, cancellationToken);
            await _orderRepository.SaveChangesAsync(cancellationToken);
            
            // Publish domain event for async processing
            order.AddDomainEvent(new OrderCreatedEvent(order.Id, customer.Email, total));
            
            _logger.LogInformation(
                "Order {OrderId} created successfully for customer {CustomerId}. Total: {Total:C}",
                order.Id,
                customer.Id,
                total);
            
            _metrics.RecordOrderCreated(total, order.Items.Count);
            
            return new OrderResponse
            {
                Id = order.Id,
                OrderNumber = order.OrderNumber,
                Total = total,
                Status = order.Status,
                EstimatedDelivery = order.EstimatedDeliveryDate
            };
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Failed to create order for customer {CustomerId}", request.CustomerId);
            return Error.Failure("An unexpected error occurred");
        }
    }
    
    private async Task<decimal> ApplyDiscounts(string? couponCode, decimal subtotal, Customer customer)
    {
        var discount = 0m;
        
        // Apply coupon if valid
        if (!string.IsNullOrEmpty(couponCode))
        {
            var coupon = await _couponRepository.GetByCodeAsync(couponCode);
            if (coupon?.IsValid == true)
            {
                discount += coupon.CalculateDiscount(subtotal);
            }
        }
        
        // Apply loyalty discount
        if (customer.LoyaltyTier == LoyaltyTier.Gold)
            discount += subtotal * 0.05m;
        else if (customer.LoyaltyTier == LoyaltyTier.Silver)
            discount += subtotal * 0.02m;
        
        return Math.Min(discount, subtotal * 0.2m); // Max 20% discount
    }
}
```

**Benefits Summary**:

![**Benefits Summary**:](images/table_08_benefits-summary.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_08_benefits-summary.md)


---

### 4.2 Anti-Patterns 2-3: Validation & Exception Handling

#### Problem Analysis

**Anti-Pattern #2: No Input Validation**

**Definition**: Accepting raw user input without any validation, leading to data corruption, security vulnerabilities, and poor user experience.

**The Story**: During the incident, I found that the checkout endpoint accepted any JSON structure. A malformed request with a negative quantity made it all the way to the database, causing a constraint violation that took the entire order processing system offline.

**Real-World Consequences**:

![**Real-World Consequences**:](images/table_09_real-world-consequences.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_09_real-world-consequences.md)


**Anti-Pattern #3: Returning Raw Exceptions**

**Definition**: Exposing internal exception details, stack traces, and system information to API clients.

**The Story**: The API was returning full stack traces to clients, including database connection strings and internal IP addresses. This was a security violation and also confused frontend developers who couldn't distinguish between validation errors and system failures.

**Real-World Consequences**:

![**Real-World Consequences**:](images/table_10_real-world-consequences.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_10_real-world-consequences.md)


#### Architectural Solution

**Design Pattern**: Global Exception Handling with Problem Details (RFC 7807)

**Complete Implementation**:

```csharp
// Program.cs - Global Exception Handling Configuration
public static class ExceptionHandlingConfiguration
{
    /// <summary>
    /// Configures global exception handling with RFC 7807 Problem Details.
    /// .NET 10 provides built-in ProblemDetails support with enhanced customization.
    /// </summary>
    public static IServiceCollection AddGlobalExceptionHandling(this IServiceCollection services)
    {
        // .NET 10 advantage: Built-in ProblemDetails support with automatic mapping
        services.AddProblemDetails(options =>
        {
            // Customize ProblemDetails for all responses
            options.CustomizeProblemDetails = ctx =>
            {
                // Add correlation ID for tracing across services
                ctx.ProblemDetails.Extensions["traceId"] = 
                    ctx.HttpContext.TraceIdentifier;
                
                // Add instance path for debugging
                ctx.ProblemDetails.Extensions["instance"] = 
                    $"{ctx.HttpContext.Request.Method} {ctx.HttpContext.Request.Path}";
                
                // Add request ID for log correlation
                ctx.ProblemDetails.Extensions["requestId"] = 
                    ctx.HttpContext.Request.Headers["X-Request-ID"].FirstOrDefault() 
                    ?? Guid.NewGuid().ToString();
                
                // Add timestamp for auditing
                ctx.ProblemDetails.Extensions["timestamp"] = 
                    DateTimeOffset.UtcNow.ToString("o");
            };
        });
        
        // Configure API behavior options for model validation
        services.Configure<ApiBehaviorOptions>(options =>
        {
            // Override default model validation response
            options.InvalidModelStateResponseFactory = context =>
            {
                // Extract validation errors
                var errors = context.ModelState
                    .Where(x => x.Value?.Errors.Count > 0)
                    .ToDictionary(
                        kvp => kvp.Key,
                        kvp => kvp.Value?.Errors.Select(e => e.ErrorMessage).ToArray()
                    );
                
                // Create RFC 7807 compliant validation problem details
                var problemDetails = new ValidationProblemDetails(errors!)
                {
                    Type = "https://tools.ietf.org/html/rfc9110#section-15.5.1",
                    Title = "Validation Error",
                    Status = StatusCodes.Status400BadRequest,
                    Detail = "One or more validation errors occurred.",
                    Instance = context.HttpContext.Request.Path
                };
                
                // Add additional context
                problemDetails.Extensions["traceId"] = context.HttpContext.TraceIdentifier;
                problemDetails.Extensions["validationTime"] = DateTime.UtcNow;
                
                return new BadRequestObjectResult(problemDetails);
            };
        });
        
        // Register custom exception to status code mapping
        services.AddSingleton<IExceptionToStatusCodeMapper, ExceptionToStatusCodeMapper>();
        
        return services;
    }
    
    /// <summary>
    /// Adds global exception handling middleware to the pipeline.
    /// </summary>
    public static IApplicationBuilder UseGlobalExceptionHandling(this IApplicationBuilder app)
    {
        var environment = app.ApplicationServices.GetRequiredService<IWebHostEnvironment>();
        
        if (environment.IsDevelopment())
        {
            // Development: Show detailed error pages with stack traces
            app.UseDeveloperExceptionPage();
            
            // Also add custom exception handler for consistent format
            app.UseExceptionHandler(exceptionHandlerApp =>
            {
                exceptionHandlerApp.Run(async context =>
                {
                    var exception = context.Features.Get<IExceptionHandlerFeature>()?.Error;
                    var logger = context.RequestServices.GetRequiredService<ILoggerFactory>()
                        .CreateLogger("DeveloperExceptionHandler");
                    
                    logger.LogError(exception, "Development exception occurred");
                    
                    // Still return structured error but with details
                    await WriteProblemDetails(context, exception, includeDetails: true);
                });
            });
        }
        else
        {
            // Production: Return sanitized RFC 7807 responses
            app.UseExceptionHandler(exceptionHandlerApp =>
            {
                exceptionHandlerApp.Run(async context =>
                {
                    var exception = context.Features.Get<IExceptionHandlerFeature>()?.Error;
                    var logger = context.RequestServices.GetRequiredService<ILoggerFactory>()
                        .CreateLogger("GlobalExceptionHandler");
                    
                    // Log full exception with context
                    logger.LogError(exception, 
                        "Unhandled exception occurred processing {Method} {Path}", 
                        context.Request.Method,
                        context.Request.Path);
                    
                    // Map exception to appropriate status code
                    var statusCode = MapExceptionToStatusCode(exception);
                    
                    // Create sanitized problem details (no stack trace)
                    var problemDetails = new ProblemDetails
                    {
                        Type = GetErrorTypeUri(statusCode),
                        Title = GetErrorTitle(statusCode),
                        Status = statusCode,
                        Detail = GetSanitizedErrorMessage(exception),
                        Instance = context.Request.Path
                    };
                    
                    // Add correlation identifiers
                    problemDetails.Extensions["traceId"] = context.TraceIdentifier;
                    problemDetails.Extensions["requestId"] = context.Request.Headers["X-Request-ID"].FirstOrDefault();
                    
                    // Add retry information for certain status codes
                    if (statusCode == StatusCodes.Status429TooManyRequests)
                    {
                        problemDetails.Extensions["retryAfter"] = 60;
                    }
                    
                    context.Response.StatusCode = statusCode;
                    context.Response.ContentType = "application/problem+json";
                    
                    await context.Response.WriteAsJsonAsync(problemDetails);
                });
            });
        }
        
        return app;
    }
    
    private static int MapExceptionToStatusCode(Exception? exception)
    {
        return exception switch
        {
            NotFoundException => StatusCodes.Status404NotFound,
            ValidationException => StatusCodes.Status400BadRequest,
            UnauthorizedException => StatusCodes.Status401Unauthorized,
            ForbiddenException => StatusCodes.Status403Forbidden,
            ConflictException => StatusCodes.Status409Conflict,
            RateLimitExceededException => StatusCodes.Status429TooManyRequests,
            BusinessRuleException => StatusCodes.Status422UnprocessableEntity,
            _ => StatusCodes.Status500InternalServerError
        };
    }
    
    private static string GetErrorTypeUri(int statusCode) => statusCode switch
    {
        StatusCodes.Status400BadRequest => "https://tools.ietf.org/html/rfc9110#section-15.5.1",
        StatusCodes.Status401Unauthorized => "https://tools.ietf.org/html/rfc9110#section-15.5.2",
        StatusCodes.Status403Forbidden => "https://tools.ietf.org/html/rfc9110#section-15.5.4",
        StatusCodes.Status404NotFound => "https://tools.ietf.org/html/rfc9110#section-15.5.5",
        StatusCodes.Status409Conflict => "https://tools.ietf.org/html/rfc9110#section-15.5.10",
        StatusCodes.Status422UnprocessableEntity => "https://tools.ietf.org/html/rfc9110#section-15.5.21",
        StatusCodes.Status429TooManyRequests => "https://tools.ietf.org/html/rfc6585#section-4",
        _ => "https://tools.ietf.org/html/rfc9110#section-15.6.1"
    };
    
    private static string GetErrorTitle(int statusCode) => statusCode switch
    {
        StatusCodes.Status400BadRequest => "Bad Request",
        StatusCodes.Status401Unauthorized => "Unauthorized",
        StatusCodes.Status403Forbidden => "Forbidden",
        StatusCodes.Status404NotFound => "Not Found",
        StatusCodes.Status409Conflict => "Conflict",
        StatusCodes.Status422UnprocessableEntity => "Unprocessable Entity",
        StatusCodes.Status429TooManyRequests => "Too Many Requests",
        _ => "Internal Server Error"
    };
    
    private static string GetSanitizedErrorMessage(Exception? exception)
    {
        if (exception == null) return "An error occurred processing your request.";
        
        // Return business-friendly messages for known exceptions
        return exception switch
        {
            NotFoundException ex => ex.Message,
            ValidationException ex => ex.Message,
            BusinessRuleException ex => ex.Message,
            _ => "An internal error occurred. Our team has been notified."
        };
    }
    
    private static async Task WriteProblemDetails(
        HttpContext context, 
        Exception? exception, 
        bool includeDetails)
    {
        context.Response.StatusCode = StatusCodes.Status500InternalServerError;
        context.Response.ContentType = "application/problem+json";
        
        var problemDetails = new ProblemDetails
        {
            Type = "https://tools.ietf.org/html/rfc9110#section-15.6.1",
            Title = includeDetails ? exception?.GetType().Name : "Internal Server Error",
            Status = StatusCodes.Status500InternalServerError,
            Detail = includeDetails ? exception?.Message : "An error occurred processing your request.",
            Instance = context.Request.Path
        };
        
        if (includeDetails && exception != null)
        {
            problemDetails.Extensions["stackTrace"] = exception.StackTrace;
            problemDetails.Extensions["source"] = exception.Source;
        }
        
        problemDetails.Extensions["traceId"] = context.TraceIdentifier;
        
        await context.Response.WriteAsJsonAsync(problemDetails);
    }
}
```

**FluentValidation Implementation**:

```csharp
// Command Validator - Comprehensive validation rules
public class CreateOrderCommandValidator : AbstractValidator<CreateOrderCommand>
{
    private readonly ICustomerRepository _customerRepository;
    private readonly IProductRepository _productRepository;
    
    public CreateOrderCommandValidator(
        ICustomerRepository customerRepository,
        IProductRepository productRepository)
    {
        _customerRepository = customerRepository;
        _productRepository = productRepository;
        
        // Customer validation with async rule
        RuleFor(x => x.CustomerId)
            .NotEmpty()
            .WithMessage("Customer ID is required")
            .WithErrorCode("CUSTOMER_REQUIRED")
            .MustAsync(BeValidCustomer)
            .WithMessage("Customer does not exist or is inactive")
            .WithErrorCode("INVALID_CUSTOMER");
        
        // Items validation
        RuleFor(x => x.Items)
            .NotEmpty()
            .WithMessage("Order must contain at least one item")
            .WithErrorCode("EMPTY_ORDER");
        
        RuleForEach(x => x.Items)
            .ChildRules(item =>
            {
                // Product existence validation
                item.RuleFor(i => i.ProductId)
                    .NotEmpty()
                    .WithMessage("Product ID is required for each item")
                    .MustAsync(BeValidProduct)
                    .WithMessage("Product does not exist")
                    .WithErrorCode("INVALID_PRODUCT");
                
                // Quantity validation
                item.RuleFor(i => i.Quantity)
                    .GreaterThan(0)
                    .WithMessage("Quantity must be greater than 0")
                    .WithErrorCode("QUANTITY_MINIMUM")
                    .LessThanOrEqualTo(100)
                    .WithMessage("Maximum quantity per item is 100")
                    .WithErrorCode("QUANTITY_MAXIMUM");
                
                // Price validation (must match current price)
                item.RuleFor(i => i.UnitPrice)
                    .MustAsync(async (cmd, price, ctx, ct) =>
                    {
                        var product = await _productRepository.GetByIdAsync(cmd.ProductId, ct);
                        ctx.MessageFormatter.AppendArgument("CurrentPrice", product?.Price ?? 0);
                        return Math.Abs(price - (product?.Price ?? 0)) < 0.01m;
                    })
                    .WithMessage("Unit price does not match current product price. Expected {CurrentPrice:C}")
                    .WithErrorCode("PRICE_MISMATCH");
            });
        
        // Shipping address validation
        RuleFor(x => x.ShippingAddress)
            .NotNull()
            .WithMessage("Shipping address is required")
            .SetValidator(new ShippingAddressValidator());
        
        // Payment method validation
        RuleFor(x => x.PaymentMethod)
            .NotNull()
            .WithMessage("Payment method is required")
            .SetValidator(new PaymentMethodValidator());
        
        // Coupon validation if provided
        RuleFor(x => x.CouponCode)
            .MustAsync(BeValidCoupon)
            .When(x => !string.IsNullOrEmpty(x.CouponCode))
            .WithMessage("Invalid or expired coupon code")
            .WithErrorCode("INVALID_COUPON");
        
        // Complex cross-property validation
        RuleFor(x => x)
            .MustAsync(HaveSufficientCredit)
            .WithMessage("Insufficient credit limit")
            .WithErrorCode("INSUFFICIENT_CREDIT");
    }
    
    private async Task<bool> BeValidCustomer(Guid customerId, CancellationToken ct)
    {
        var customer = await _customerRepository.GetByIdAsync(customerId, ct);
        return customer?.IsActive == true;
    }
    
    private async Task<bool> BeValidProduct(Guid productId, CancellationToken ct)
    {
        return await _productRepository.ExistsAsync(productId, ct);
    }
    
    private async Task<bool> BeValidCoupon(string? couponCode, CancellationToken ct)
    {
        if (string.IsNullOrEmpty(couponCode)) return true;
        var coupon = await _couponRepository.GetByCodeAsync(couponCode, ct);
        return coupon?.IsValid == true;
    }
    
    private async Task<bool> HaveSufficientCredit(CreateOrderCommand command, CancellationToken ct)
    {
        var customer = await _customerRepository.GetByIdAsync(command.CustomerId, ct);
        if (customer?.CreditLimit == null) return true;
        
        var subtotal = command.Items.Sum(i => i.UnitPrice * i.Quantity);
        return subtotal <= customer.CreditLimit;
    }
}

// Reusable validator for complex types
public class ShippingAddressValidator : AbstractValidator<ShippingAddressDto>
{
    public ShippingAddressValidator()
    {
        RuleFor(x => x.Street)
            .NotEmpty()
            .MaximumLength(200);
        
        RuleFor(x => x.City)
            .NotEmpty()
            .MaximumLength(100);
        
        RuleFor(x => x.PostalCode)
            .Matches(@"^\d{5}(-\d{4})?$")
            .WithMessage("Invalid postal code format");
        
        RuleFor(x => x.Country)
            .NotEmpty()
            .Must(BeValidCountry)
            .WithMessage("Country not in supported list");
    }
    
    private bool BeValidCountry(string country)
    {
        var supportedCountries = new[] { "USA", "Canada", "Mexico" };
        return supportedCountries.Contains(country);
    }
}
```

**Custom Exception Types**:

```csharp
// Domain-specific exceptions for clean error handling
public abstract class DomainException : Exception
{
    public string ErrorCode { get; }
    public int StatusCode { get; }
    
    protected DomainException(string message, string errorCode, int statusCode) 
        : base(message)
    {
        ErrorCode = errorCode;
        StatusCode = statusCode;
    }
}

public class NotFoundException : DomainException
{
    public NotFoundException(string entityName, object id) 
        : base($"{entityName} with ID '{id}' was not found.", 
               "NOT_FOUND", 
               StatusCodes.Status404NotFound)
    {
    }
}

public class ValidationException : DomainException
{
    public Dictionary<string, string[]> Errors { get; }
    
    public ValidationException(string message, Dictionary<string, string[]> errors) 
        : base(message, "VALIDATION_ERROR", StatusCodes.Status400BadRequest)
    {
        Errors = errors;
    }
}

public class BusinessRuleException : DomainException
{
    public BusinessRuleException(string ruleName, string message) 
        : base(message, ruleName.ToUpper(), StatusCodes.Status422UnprocessableEntity)
    {
    }
}

public class ConflictException : DomainException
{
    public ConflictException(string message, string conflictId) 
        : base(message, "CONFLICT", StatusCodes.Status409Conflict)
    {
        Data["ConflictId"] = conflictId;
    }
}
```

**Benefits Summary**:

![**Benefits Summary**:](images/table_11_benefits-summary.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_11_benefits-summary.md)


---

### 4.3 Anti-Patterns 4-5: Async & Cancellation

#### Problem Analysis

**Anti-Pattern #4: Blocking Async with .Result or .Wait()**

**Definition**: Using `.Result`, `.Wait()`, or `.GetAwaiter().GetResult()` on async methods, blocking threads and causing deadlocks.

**The Story**: During the incident, the thread pool had 4,500 threads—all blocked. The system was processing fewer than 500 requests per second despite having 16 CPU cores. Each request was blocking a thread while waiting for I/O. Under load, the thread pool exhausted, and new requests were queued until timeouts occurred.

**Thread Pool Starvation Mechanics**:

```mermaid
```

![**Thread Pool Starvation Mechanics**:](images/diagram_06_thread-pool-starvation-mechanics-a815.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/diagram_06_thread-pool-starvation-mechanics-a815.md)


**Consequences**:
- **Deadlocks**: In ASP.NET Core with `SynchronizationContext`, `.Result` can cause deadlocks
- **Thread Pool Exhaustion**: Blocked threads can't serve new requests
- **Scalability Collapse**: System fails under moderate load
- **Memory Pressure**: Queued requests accumulate, increasing memory usage

**Anti-Pattern #5: Ignoring CancellationTokens**

**Definition**: Not accepting or propagating `CancellationToken` through async operations, wasting resources on abandoned requests.

**The Story**: A user opened the checkout page, then closed their browser tab. The server continued processing the order for another 30 seconds, holding database connections and CPU resources. With thousands of abandoned requests during the sale, database connection pools exhausted.

**Resource Waste Example**:
- Client closes browser tab → Server continues processing
- Network timeout → Database query continues running
- User navigates away → API still processes expensive operation

#### Architectural Solution

**Complete Implementation**:

```csharp
namespace ECommerce.Application.Handlers;

/// <summary>
/// Handler demonstrating proper async/await with cancellation token propagation.
/// .NET 10 advantages:
/// - Native IAsyncEnumerable support for streaming
/// - Improved async disposal patterns
/// - Better integration with OpenTelemetry for cancellation tracking
/// </summary>
public class GetCustomerOrdersHandler : IRequestHandler<GetCustomerOrdersQuery, PagedResult<OrderDto>>
{
    private readonly AppDbContext _context;
    private readonly ILogger<GetCustomerOrdersHandler> _logger;
    private readonly ICacheService _cache;
    private readonly IAsyncPolicy<OrderDto[]> _circuitBreaker;

    public GetCustomerOrdersHandler(
        AppDbContext context,
        ILogger<GetCustomerOrdersHandler> logger,
        ICacheService cache,
        IAsyncPolicy<OrderDto[]> circuitBreaker)
    {
        _context = context;
        _logger = logger;
        _cache = cache;
        _circuitBreaker = circuitBreaker;
    }

    /// <summary>
    /// Demonstrates proper async/await with cancellation token propagation.
    /// Every async call receives the cancellation token to enable early termination.
    /// </summary>
    public async Task<PagedResult<OrderDto>> Handle(
        GetCustomerOrdersQuery request,
        CancellationToken cancellationToken)
    {
        // Create activity for distributed tracing
        using var activity = DiagnosticsConfig.ActivitySource.StartActivity(
            "GetCustomerOrders",
            ActivityKind.Server);
        
        // Add custom tags to trace
        activity?.SetTag("customer.id", request.CustomerId);
        activity?.SetTag("page", request.Page);
        activity?.SetTag("page.size", request.PageSize);
        
        // Check if cancellation already requested
        cancellationToken.ThrowIfCancellationRequested();
        
        _logger.LogInformation(
            "Retrieving orders for customer {CustomerId} with page {Page}",
            request.CustomerId,
            request.Page);
        
        // Async cache check with cancellation
        var cacheKey = $"customer_orders:{request.CustomerId}:{request.Page}";
        var cached = await _cache.GetAsync<PagedResult<OrderDto>>(
            cacheKey, 
            cancellationToken); // Token passed through
        
        if (cached != null)
        {
            _logger.LogDebug("Cache hit for {CacheKey}", cacheKey);
            activity?.SetTag("cache.hit", true);
            return cached;
        }
        
        activity?.SetTag("cache.hit", false);
        
        // Build query with IQueryable - deferred execution
        var query = _context.Orders
            .AsNoTracking() // No change tracking for read-only queries
            .Where(o => o.CustomerId == request.CustomerId)
            .Where(o => !o.IsDeleted);
        
        // Apply filters with expression trees
        if (request.Status.HasValue)
        {
            query = query.Where(o => o.Status == request.Status.Value);
        }
        
        if (request.FromDate.HasValue)
        {
            query = query.Where(o => o.OrderDate >= request.FromDate.Value);
        }
        
        if (request.ToDate.HasValue)
        {
            query = query.Where(o => o.OrderDate <= request.ToDate.Value);
        }
        
        // Apply sorting with dynamic expression
        query = request.SortDescending
            ? query.OrderByDescending(GetSortProperty(request.SortBy))
            : query.OrderBy(GetSortProperty(request.SortBy));
        
        // Execute count query with cancellation
        var totalCount = await query.CountAsync(cancellationToken);
        
        // Execute paginated query with projection
        // .NET 10: EF Core generates optimal SQL with only needed columns
        var items = await query
            .Skip((request.Page - 1) * request.PageSize)
            .Take(request.PageSize)
            .Select(o => new OrderDto
            {
                Id = o.Id,
                OrderNumber = o.OrderNumber,
                Total = o.Total,
                Status = o.Status,
                OrderDate = o.OrderDate,
                ItemCount = o.Items.Count(),
                LatestStatusDate = o.StatusHistory
                    .OrderByDescending(sh => sh.CreatedAt)
                    .Select(sh => sh.CreatedAt)
                    .FirstOrDefault()
            })
            .ToListAsync(cancellationToken); // Token ensures query cancellation if client disconnects
        
        var result = new PagedResult<OrderDto>
        {
            Items = items,
            TotalCount = totalCount,
            Page = request.Page,
            PageSize = request.PageSize,
            HasNextPage = totalCount > request.Page * request.PageSize
        };
        
        // Async cache set with cancellation
        await _cache.SetAsync(
            cacheKey, 
            result, 
            TimeSpan.FromMinutes(5), 
            cancellationToken);
        
        return result;
    }
    
    /// <summary>
    /// Demonstrates IAsyncEnumerable for streaming large datasets.
    /// .NET 10 advantage: Native async streaming with cancellation support.
    /// </summary>
    public async IAsyncEnumerable<OrderExportDto> ExportOrdersAsync(
        ExportOrdersQuery request,
        [EnumeratorCancellation] CancellationToken cancellationToken)
    {
        // Stream results one by one without loading all into memory
        await foreach (var order in _context.Orders
            .AsNoTracking()
            .Where(o => o.OrderDate >= request.FromDate)
            .OrderBy(o => o.OrderDate)
            .AsAsyncEnumerable()
            .WithCancellation(cancellationToken))
        {
            // Yield each result as it's available
            yield return new OrderExportDto
            {
                OrderNumber = order.OrderNumber,
                CustomerName = order.Customer.FullName,
                Total = order.Total,
                OrderDate = order.OrderDate
            };
            
            // Check cancellation between yields
            cancellationToken.ThrowIfCancellationRequested();
        }
    }
    
    /// <summary>
    /// Demonstrates proper async disposal pattern.
    /// .NET 10: IAsyncDisposable for cleanup operations.
    /// </summary>
    private Expression<Func<Order, object>> GetSortProperty(string sortBy) => sortBy?.ToLower() switch
    {
        "orderdate" => o => o.OrderDate,
        "total" => o => o.Total,
        "ordernumber" => o => o.OrderNumber,
        _ => o => o.OrderDate
    };
}

/// <summary>
/// Demonstrates parallel async operations with proper concurrency control.
/// </summary>
public class BulkOrderProcessor : IAsyncDisposable
{
    private readonly SemaphoreSlim _semaphore = new(10, 10); // Max 10 concurrent operations
    private readonly ILogger<BulkOrderProcessor> _logger;
    private readonly HttpClient _httpClient;
    
    public BulkOrderProcessor(ILogger<BulkOrderProcessor> logger, IHttpClientFactory httpClientFactory)
    {
        _logger = logger;
        _httpClient = httpClientFactory.CreateClient("ExternalApi");
    }
    
    /// <summary>
    /// Processes multiple orders with controlled concurrency.
    /// Demonstrates proper async patterns for batch operations.
    /// </summary>
    public async Task<IReadOnlyList<ProcessResult>> ProcessOrdersAsync(
        IEnumerable<Guid> orderIds,
        CancellationToken cancellationToken)
    {
        var tasks = new List<Task<ProcessResult>>();
        
        foreach (var orderId in orderIds)
        {
            // Wait for semaphore before starting new task
            await _semaphore.WaitAsync(cancellationToken);
            
            tasks.Add(ProcessOrderAsync(orderId, cancellationToken)
                .ContinueWith(t =>
                {
                    _semaphore.Release();
                    return t.Result;
                }, cancellationToken));
        }
        
        // Wait for all tasks to complete
        var results = await Task.WhenAll(tasks);
        return results;
    }
    
    private async Task<ProcessResult> ProcessOrderAsync(
        Guid orderId,
        CancellationToken cancellationToken)
    {
        // Use Polly for resilience
        var response = await Policy
            .Handle<HttpRequestException>()
            .Or<TaskCanceledException>()
            .WaitAndRetryAsync(
                3,
                retryAttempt => TimeSpan.FromSeconds(Math.Pow(2, retryAttempt)),
                onRetry: (exception, timeSpan, retryCount, context) =>
                {
                    _logger.LogWarning(
                        exception,
                        "Retry {RetryCount} for order {OrderId} after {Delay}ms",
                        retryCount,
                        orderId,
                        timeSpan.TotalMilliseconds);
                })
            .ExecuteAsync(async () =>
            {
                // Pass cancellation token to HttpClient
                var response = await _httpClient.PostAsJsonAsync(
                    $"api/orders/{orderId}/process",
                    new { Action = "Process" },
                    cancellationToken);
                
                response.EnsureSuccessStatusCode();
                return await response.Content.ReadFromJsonAsync<ProcessResult>(cancellationToken);
            });
        
        return response;
    }
    
    /// <summary>
    /// .NET 10: IAsyncDisposable for proper cleanup.
    /// </summary>
    public async ValueTask DisposeAsync()
    {
        _semaphore.Dispose();
        _httpClient.Dispose();
        await Task.CompletedTask;
    }
}
```

**Performance Comparison**:

```mermaid
```

![**Performance Comparison**:](images/diagram_07_performance-comparison.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/diagram_07_performance-comparison.md)


**Benefits Summary**:

![**Benefits Summary**:](images/table_12_benefits-summary.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_12_benefits-summary.md)


---

### 4.4 Anti-Pattern 11: Observability

#### Problem Analysis

**Definition**: Zero visibility into application behavior, making debugging, performance analysis, and capacity planning impossible.

**The Story**: When the incident occurred, we had no idea what was happening. There were no traces to show which requests were slow. Logs were simple `Console.WriteLine` statements that provided no context. Metrics didn't exist. We spent two hours reproducing the issue locally before we could even begin to diagnose it.

**Observability Gap Consequences**:

![**Observability Gap Consequences**:](images/table_13_observability-gap-consequences.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_13_observability-gap-consequences.md)


#### Architectural Solution

```csharp
namespace ECommerce.API.Configuration;

/// <summary>
/// Comprehensive observability configuration with OpenTelemetry and Serilog.
/// .NET 10 advantages:
/// - Native OpenTelemetry integration
/// - Built-in metrics with System.Diagnostics.Metrics
/// - Enhanced activity APIs for distributed tracing
/// </summary>
public static class ObservabilityConfiguration
{
    public static IServiceCollection AddObservability(
        this IServiceCollection services, 
        IConfiguration configuration)
    {
        // 1. Configure Serilog for structured logging
        Log.Logger = new LoggerConfiguration()
            .ReadFrom.Configuration(configuration)
            .Enrich.WithProperty("Application", "ECommerceAPI")
            .Enrich.WithProperty("Environment", Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"))
            .Enrich.WithMachineName()
            .Enrich.WithThreadId()
            .Enrich.WithProcessId()
            .Enrich.WithEnvironmentUserName()
            .Enrich.WithCorrelationId()
            .Enrich.FromLogContext()
            .WriteTo.Console(new JsonFormatter()) // JSON for log aggregation
            .WriteTo.Seq(configuration["Seq:Url"] ?? "http://localhost:5341")
            .WriteTo.OpenTelemetry()
            .WriteTo.File(
                "logs/ecommerce-.log",
                rollingInterval: RollingInterval.Day,
                retainedFileCountLimit: 7,
                outputTemplate: "{Timestamp:yyyy-MM-dd HH:mm:ss.fff zzz} [{Level:u3}] {Message:lj}{NewLine}{Exception}")
            .CreateLogger();
        
        services.AddSerilog();
        
        // 2. Configure OpenTelemetry for metrics and traces
        services.AddOpenTelemetry()
            .ConfigureResource(resource => resource
                .AddService(
                    serviceName: "ecommerce-api",
                    serviceVersion: "1.0.0",
                    serviceInstanceId: Environment.MachineName)
                .AddAttributes(new Dictionary<string, object>
                {
                    ["deployment.environment"] = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") ?? "development",
                    ["cloud.region"] = configuration["Cloud:Region"] ?? "unknown",
                    ["service.namespace"] = "ecommerce"
                }))
            .WithMetrics(metrics => metrics
                // .NET 10 built-in metrics
                .AddRuntimeInstrumentation()
                .AddProcessInstrumentation()
                .AddAspNetCoreInstrumentation()
                // HTTP client metrics
                .AddHttpClientInstrumentation()
                // EF Core metrics
                .AddEntityFrameworkCoreInstrumentation()
                // Redis metrics
                .AddRedisInstrumentation()
                // Custom meters from our application
                .AddMeter("ECommerce.API", "ECommerce.Application", "ECommerce.Infrastructure")
                // Exporters
                .AddPrometheusExporter(options =>
                {
                    options.ScrapeEndpointPath = "/metrics";
                })
                .AddOtlpExporter(options =>
                {
                    options.Endpoint = new Uri(configuration["Otlp:Endpoint"] ?? "http://localhost:4317");
                    options.Protocol = OtlpExportProtocol.Grpc;
                }))
            .WithTracing(tracing => tracing
                .AddSource("ECommerce.API", "ECommerce.Application", "ECommerce.Infrastructure")
                .AddAspNetCoreInstrumentation(options =>
                {
                    options.RecordException = true;
                    options.Filter = context => 
                        !context.Request.Path.StartsWithSegments("/health") &&
                        !context.Request.Path.StartsWithSegments("/metrics");
                    options.EnrichWithHttpRequest = (activity, request) =>
                    {
                        activity.SetTag("http.user_agent", request.Headers.UserAgent.FirstOrDefault());
                        activity.SetTag("http.client_ip", request.HttpContext.Connection.RemoteIpAddress);
                        activity.SetTag("http.request_id", request.HttpContext.TraceIdentifier);
                    };
                    options.EnrichWithHttpResponse = (activity, response) =>
                    {
                        activity.SetTag("http.response_size", response.ContentLength);
                        activity.SetTag("http.status_code", response.StatusCode);
                    };
                })
                .AddHttpClientInstrumentation(options =>
                {
                    options.RecordException = true;
                    options.EnrichWithHttpRequestMessage = (activity, request) =>
                    {
                        activity.SetTag("http.request_uri", request.RequestUri);
                        activity.SetTag("http.request_method", request.Method);
                    };
                })
                .AddEntityFrameworkCoreInstrumentation(options =>
                {
                    options.SetDbStatementForText = true;
                    options.SetDbStatementForStoredProcedure = true;
                    options.EnableConnectionLevelAttributes = true;
                })
                .AddRedisInstrumentation()
                .AddSqlClientInstrumentation()
                .AddGrpcClientInstrumentation()
                .AddOtlpExporter(options =>
                {
                    options.Endpoint = new Uri(configuration["Otlp:Endpoint"] ?? "http://localhost:4317");
                    options.Protocol = OtlpExportProtocol.Grpc;
                })
                .AddConsoleExporter()); // Development only
        
        // 3. Health checks for readiness/liveness probes
        services.AddHealthChecks()
            .AddDbContextCheck<AppDbContext>(
                "database",
                failureStatus: HealthStatus.Degraded,
                tags: new[] { "ready", "live" })
            .AddRedis(
                configuration["Redis:ConnectionString"],
                "redis",
                failureStatus: HealthStatus.Degraded,
                tags: new[] { "ready" })
            .AddUrlGroup(
                new Uri(configuration["Seq:Url"] ?? "http://localhost:5341"),
                "seq",
                HealthStatus.Degraded,
                tags: new[] { "ready" })
            .AddProcessAllocatedMemoryHealthCheck(
                512 * 1024 * 1024, // 512MB threshold
                "memory",
                tags: new[] { "live" });
        
        // 4. Configure diagnostics for better debugging
        services.AddProblemDetails();
        services.AddHttpContextAccessor();
        
        // 5. Add custom metrics and meters
        services.AddSingleton<OrderMetrics>();
        services.AddSingleton<PerformanceMetrics>();
        
        return services;
    }
}

/// <summary>
/// Custom business metrics for order processing.
/// Demonstrates .NET 10's System.Diagnostics.Metrics API.
/// </summary>
public class OrderMetrics
{
    private readonly Counter<int> _orderCreatedCounter;
    private readonly Histogram<double> _orderProcessingTime;
    private readonly UpDownCounter<int> _activeOrdersGauge;
    private readonly Counter<long> _orderValueCounter;
    private readonly ObservableGauge<int> _pendingOrdersGauge;
    private readonly ILogger<OrderMetrics> _logger;

    public OrderMetrics(IMeterFactory meterFactory, ILogger<OrderMetrics> logger)
    {
        _logger = logger;
        
        var meter = meterFactory.Create("ECommerce.Application.Orders");
        
        // Counter: Total orders created (increases only)
        _orderCreatedCounter = meter.CreateCounter<int>(
            "orders.created.total",
            description: "Total number of orders created");
        
        // Histogram: Order processing time distribution
        _orderProcessingTime = meter.CreateHistogram<double>(
            "orders.processing.time",
            unit: "ms",
            description: "Time taken to process orders");
        
        // UpDownCounter: Current active orders (increases and decreases)
        _activeOrdersGauge = meter.CreateUpDownCounter<int>(
            "orders.active",
            description: "Number of active orders being processed");
        
        // Counter with dimensions for monetary tracking
        _orderValueCounter = meter.CreateCounter<long>(
            "orders.value.total",
            unit: "cents",
            description: "Total order value in cents");
        
        // ObservableGauge: Automatic metric from data source
        _pendingOrdersGauge = meter.CreateObservableGauge<int>(
            "orders.pending",
            () => GetPendingOrdersCount(),
            description: "Number of pending orders awaiting processing");
    }

    public void RecordOrderCreated(decimal totalAmount, int itemCount)
    {
        _orderCreatedCounter.Add(1, 
            new KeyValuePair<string, object?>("item_count", itemCount),
            new KeyValuePair<string, object?>("success", true));
        
        _orderValueCounter.Add((long)(totalAmount * 100)); // Convert to cents for integer storage
        
        _logger.LogDebug("Recorded order creation: {Amount:C}, {Items} items", totalAmount, itemCount);
    }

    public IDisposable MeasureOrderProcessingTime()
    {
        return _orderProcessingTime.Measure();
    }
    
    public void IncrementActiveOrders(int customerId)
    {
        _activeOrdersGauge.Add(1, 
            new KeyValuePair<string, object?>("customer_tier", GetCustomerTier(customerId)));
    }
    
    public void DecrementActiveOrders(int customerId)
    {
        _activeOrdersGauge.Add(-1,
            new KeyValuePair<string, object?>("customer_tier", GetCustomerTier(customerId)));
    }
    
    private int GetPendingOrdersCount()
    {
        // This method is called automatically by the meter at the collection interval
        // It should return the current value quickly (e.g., from a cache)
        return PendingOrdersCache.GetCount();
    }
    
    private string GetCustomerTier(int customerId)
    {
        // In real implementation, fetch from cache or claims
        return "standard";
    }
}

/// <summary>
/// Structured logging with context in handlers.
/// </summary>
public class OrderHandler : IRequestHandler<CreateOrderCommand, OrderResponse>
{
    private readonly ILogger<OrderHandler> _logger;
    private readonly OrderMetrics _metrics;
    private readonly IHttpContextAccessor _httpContextAccessor;

    public async Task<OrderResponse> Handle(CreateOrderCommand request, CancellationToken ct)
    {
        // Start a new trace span for this operation
        using var activity = DiagnosticsConfig.ActivitySource.StartActivity(
            "ProcessOrder", 
            ActivityKind.Server);
        
        // Add custom tags to the trace for debugging
        DiagnosticsConfig.AddTags(activity,
            ("order.customer_id", request.CustomerId),
            ("order.item_count", request.Items.Count));
        
        // Add correlation ID from HTTP context
        var correlationId = _httpContextAccessor.HttpContext?.TraceIdentifier;
        
        // Structured logging with context
        using (_logger.BeginScope(new Dictionary<string, object>
        {
            ["CorrelationId"] = correlationId,
            ["CustomerId"] = request.CustomerId,
            ["Operation"] = "CreateOrder"
        }))
        {
            _logger.LogInformation(
                "Processing order for customer {CustomerId} with {ItemCount} items",
                request.CustomerId,
                request.Items.Count);
            
            // Measure execution time with custom metrics
            using var timer = _metrics.MeasureOrderProcessingTime();
            _metrics.IncrementActiveOrders(request.CustomerId);
            
            try
            {
                // Simulate business logic
                var order = await ProcessOrderLogic(request, ct);
                
                _metrics.RecordOrderCreated(order.Total, order.ItemCount);
                
                _logger.LogInformation(
                    "Order {OrderId} created successfully for customer {CustomerId}. Total: {Total:C}",
                    order.Id,
                    request.CustomerId,
                    order.Total);
                
                DiagnosticsConfig.AddTags(activity,
                    ("order.id", order.Id),
                    ("order.total", order.Total),
                    ("order.status", order.Status));
                
                return order;
            }
            catch (Exception ex)
            {
                DiagnosticsConfig.RecordException(activity, ex);
                
                _logger.LogError(ex,
                    "Failed to create order for customer {CustomerId}. Error: {ErrorMessage}",
                    request.CustomerId,
                    ex.Message);
                
                throw;
            }
            finally
            {
                _metrics.DecrementActiveOrders(request.CustomerId);
            }
        }
    }
}
```

---

## 5. Implementation Guide - Part 1

### 5.1 Program.cs Configuration - Foundation Layer

```csharp
// Program.cs - Complete .NET 10 Application Setup - Part 1 Foundation

var builder = WebApplication.CreateBuilder(args);

// ============================================================================
// 1. Logging & Observability (Anti-Pattern #11)
// ============================================================================
builder.Host.UseSerilog();
builder.Services.AddObservability(builder.Configuration);

// ============================================================================
// 2. Database Configuration
// ============================================================================
builder.Services.AddDbContext<AppDbContext>((sp, options) =>
{
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("Default"),
        sqlOptions =>
        {
            sqlOptions.EnableRetryOnFailure(3);
            sqlOptions.CommandTimeout(30);
            sqlOptions.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery);
        });
    
    // .NET 10: Enhanced logging for EF Core with structured logs
    options.LogTo(
        sp.GetRequiredService<ILoggerFactory>().CreateLogger<AppDbContext>(),
        LogLevel.Information,
        DbLoggerCategory.Database.Command.Name);
    
    options.EnableSensitiveDataLogging(builder.Environment.IsDevelopment());
    options.EnableDetailedErrors(builder.Environment.IsDevelopment());
});

// ============================================================================
// 3. MediatR with Pipeline Behaviors (Anti-Pattern #1)
// ============================================================================
builder.Services.AddMediatR(cfg =>
{
    cfg.RegisterServicesFromAssembly(typeof(CreateOrderHandler).Assembly);
    
    // Pipeline order matters for cross-cutting concerns
    cfg.AddBehavior(typeof(IPipelineBehavior<,>), typeof(LoggingBehavior<,>));
    cfg.AddBehavior(typeof(IPipelineBehavior<,>), typeof(ValidationBehavior<,>));
    cfg.AddBehavior(typeof(IPipelineBehavior<,>), typeof(PerformanceBehavior<,>));
});

// ============================================================================
// 4. Validation (Anti-Pattern #2)
// ============================================================================
builder.Services.AddValidatorsFromAssemblyContaining<CreateOrderCommandValidator>();
builder.Services.AddScoped<IValidator<CreateOrderCommand>, CreateOrderCommandValidator>();

// ============================================================================
// 5. Global Exception Handling (Anti-Pattern #3)
// ============================================================================
builder.Services.AddGlobalExceptionHandling();

// ============================================================================
// 6. API Configuration
// ============================================================================
builder.Services.AddControllers(options =>
{
    options.Filters.Add<ApiExceptionFilterAttribute>();
    options.Filters.Add<ApiResultFilterAttribute>();
    options.SuppressAsyncSuffixInActionNames = false;
    
    // Add global model validation filter
    options.Filters.Add<ValidateModelAttribute>();
})
.AddJsonOptions(options =>
{
    options.JsonSerializerOptions.PropertyNamingPolicy = JsonNamingPolicy.CamelCase;
    options.JsonSerializerOptions.DefaultIgnoreCondition = JsonIgnoreCondition.WhenWritingNull;
    options.JsonSerializerOptions.Converters.Add(new JsonStringEnumConverter());
});

// ============================================================================
// 7. API Versioning
// ============================================================================
builder.Services.AddApiVersioning(options =>
{
    options.DefaultApiVersion = new ApiVersion(1, 0);
    options.AssumeDefaultVersionWhenUnspecified = true;
    options.ReportApiVersions = true;
    options.ApiVersionReader = ApiVersionReader.Combine(
        new HeaderApiVersionReader("api-version"),
        new QueryStringApiVersionReader("api-version"),
        new UrlSegmentApiVersionReader());
});

// ============================================================================
// 8. Authentication & Authorization
// ============================================================================
builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        options.Authority = builder.Configuration["Auth:Authority"];
        options.Audience = builder.Configuration["Auth:Audience"];
        options.RequireHttpsMetadata = !builder.Environment.IsDevelopment();
        
        options.Events = new JwtBearerEvents
        {
            OnAuthenticationFailed = context =>
            {
                var logger = context.HttpContext.RequestServices.GetRequiredService<ILogger<Program>>();
                logger.LogError(context.Exception, "Authentication failed");
                return Task.CompletedTask;
            }
        };
    });

// ============================================================================
// 9. Health Checks
// ============================================================================
builder.Services.AddHealthChecks()
    .AddDbContextCheck<AppDbContext>()
    .AddRedis(builder.Configuration["Redis:ConnectionString"]);

// ============================================================================
// 10. OpenAPI (Swagger)
// ============================================================================
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo 
    { 
        Title = "ECommerce API", 
        Version = "v1",
        Description = "Anti-Pattern Free .NET 10 API - Part 1 Foundation",
        Contact = new OpenApiContact
        {
            Name = "API Support",
            Email = "support@ecommerce.com"
        }
    });
    
    c.AddSecurityDefinition("Bearer", new OpenApiSecurityScheme
    {
        In = ParameterLocation.Header,
        Description = "Please enter JWT with Bearer into field",
        Name = "Authorization",
        Type = SecuritySchemeType.ApiKey,
        Scheme = "Bearer"
    });
});

// ============================================================================
// 11. Build Application
// ============================================================================
var app = builder.Build();

// ============================================================================
// 12. Configure HTTP Pipeline
// ============================================================================

// Development tools
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "ECommerce API v1");
        c.RoutePrefix = string.Empty;
    });
}
else
{
    app.UseHsts();
    app.UseHttpsRedirection();
}

// Observability middleware
app.UseSerilogRequestLogging(options =>
{
    options.EnrichDiagnosticContext = (diagnosticContext, httpContext) =>
    {
        diagnosticContext.Set("User", httpContext.User.Identity?.Name);
        diagnosticContext.Set("RequestId", httpContext.TraceIdentifier);
    };
});

app.UseOpenTelemetryPrometheusScrapingEndpoint();

// Global exception handling
app.UseGlobalExceptionHandling();

// Security
app.UseAuthentication();
app.UseAuthorization();

// Routing
app.UseRouting();

// Health checks
app.MapHealthChecks("/health/ready", new HealthCheckOptions
{
    Predicate = check => check.Tags.Contains("ready"),
    ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
});

app.MapHealthChecks("/health/live", new HealthCheckOptions
{
    Predicate = _ => false
});

// Metrics endpoint
app.MapMetrics();

// Controllers
app.MapControllers();

// ============================================================================
// 13. Database Migration (if in development)
// ============================================================================
if (app.Environment.IsDevelopment())
{
    using var scope = app.Services.CreateScope();
    var dbContext = scope.ServiceProvider.GetRequiredService<AppDbContext>();
    await dbContext.Database.MigrateAsync();
}

// ============================================================================
// 14. Run Application
// ============================================================================
await app.RunAsync();
```

### 5.2 Testing Strategy - Foundation Layer

```csharp
// Unit test example with xUnit and Moq
public class CreateOrderHandlerTests
{
    private readonly Mock<IOrderRepository> _orderRepositoryMock;
    private readonly Mock<ICustomerRepository> _customerRepositoryMock;
    private readonly Mock<IInventoryService> _inventoryServiceMock;
    private readonly Mock<IPaymentService> _paymentServiceMock;
    private readonly Mock<ILogger<CreateOrderHandler>> _loggerMock;
    private readonly CreateOrderHandler _handler;
    
    public CreateOrderHandlerTests()
    {
        _orderRepositoryMock = new Mock<IOrderRepository>();
        _customerRepositoryMock = new Mock<ICustomerRepository>();
        _inventoryServiceMock = new Mock<IInventoryService>();
        _paymentServiceMock = new Mock<IPaymentService>();
        _loggerMock = new Mock<ILogger<CreateOrderHandler>>();
        
        _handler = new CreateOrderHandler(
            _orderRepositoryMock.Object,
            _customerRepositoryMock.Object,
            _inventoryServiceMock.Object,
            _paymentServiceMock.Object,
            _loggerMock.Object,
            Mock.Of<OrderMetrics>());
    }
    
    [Fact]
    public async Task Handle_ValidRequest_ReturnsOrderResponse()
    {
        // Arrange
        var command = new CreateOrderCommand
        {
            CustomerId = Guid.NewGuid(),
            Items = new List<OrderItemDto>
            {
                new() { ProductId = Guid.NewGuid(), Quantity = 1, UnitPrice = 10.00m }
            },
            ShippingAddress = new ShippingAddressDto(),
            PaymentMethod = new PaymentMethodDto()
        };
        
        var customer = Customer.Create("John Doe", "john@example.com");
        _customerRepositoryMock.Setup(x => x.GetByIdAsync(command.CustomerId, It.IsAny<CancellationToken>()))
            .ReturnsAsync(customer);
        
        _inventoryServiceMock.Setup(x => x.CheckAvailabilityAsync(It.IsAny<IEnumerable<(Guid, int)>>(), It.IsAny<CancellationToken>()))
            .ReturnsAsync(InventoryCheckResult.AllAvailable());
        
        _paymentServiceMock.Setup(x => x.ProcessPaymentAsync(It.IsAny<Guid>(), It.IsAny<PaymentMethodDto>(), 
                It.IsAny<decimal>(), It.IsAny<CancellationToken>()))
            .ReturnsAsync(PaymentResult.Success("txn_123"));
        
        // Act
        var result = await _handler.Handle(command, CancellationToken.None);
        
        // Assert
        Assert.True(result.IsSuccess);
        Assert.NotNull(result.Value);
        Assert.NotEqual(Guid.Empty, result.Value.Id);
        
        _orderRepositoryMock.Verify(x => x.AddAsync(It.IsAny<Order>(), It.IsAny<CancellationToken>()), Times.Once);
        _orderRepositoryMock.Verify(x => x.SaveChangesAsync(It.IsAny<CancellationToken>()), Times.Once);
    }
    
    [Fact]
    public async Task Handle_InvalidCustomer_ReturnsError()
    {
        // Arrange
        var command = new CreateOrderCommand
        {
            CustomerId = Guid.NewGuid(),
            Items = new List<OrderItemDto>()
        };
        
        _customerRepositoryMock.Setup(x => x.GetByIdAsync(command.CustomerId, It.IsAny<CancellationToken>()))
            .ReturnsAsync((Customer)null);
        
        // Act
        var result = await _handler.Handle(command, CancellationToken.None);
        
        // Assert
        Assert.True(result.IsError);
        Assert.Contains(result.Errors, e => e.Type == ErrorType.NotFound);
    }
    
    [Fact]
    public async Task Handle_OutOfStock_ReturnsError()
    {
        // Arrange
        var command = new CreateOrderCommand
        {
            CustomerId = Guid.NewGuid(),
            Items = new List<OrderItemDto>
            {
                new() { ProductId = Guid.NewGuid(), Quantity = 1, UnitPrice = 10.00m }
            }
        };
        
        var customer = Customer.Create("John Doe", "john@example.com");
        _customerRepositoryMock.Setup(x => x.GetByIdAsync(command.CustomerId, It.IsAny<CancellationToken>()))
            .ReturnsAsync(customer);
        
        _inventoryServiceMock.Setup(x => x.CheckAvailabilityAsync(It.IsAny<IEnumerable<(Guid, int)>>(), It.IsAny<CancellationToken>()))
            .ReturnsAsync(InventoryCheckResult.ProductOutOfStock(Guid.NewGuid()));
        
        // Act
        var result = await _handler.Handle(command, CancellationToken.None);
        
        // Assert
        Assert.True(result.IsError);
        Assert.Contains(result.Errors, e => e.Type == ErrorType.Validation);
    }
}
```

---

## 6. Monitoring & Observability

### 6.1 Key Metrics Dashboard - Foundation Layer

```yaml
# Grafana Dashboard Configuration - Foundation Metrics
dashboard:
  title: "E-Commerce API - Foundation Layer"
  uid: "ecommerce-foundation"
  
  panels:
    - id: 1
      title: "Request Rate & Errors"
      type: "timeseries"
      targets:
        - expr: "sum(rate(http_server_duration_ms_count[5m])) by (http_method)"
          legend: "{{http_method}} - Total"
        - expr: "sum(rate(http_server_duration_ms_count{http_status=~\"5..\"}[5m])) by (http_method)"
          legend: "{{http_method}} - Errors"
    
    - id: 2
      title: "Request Duration (p95)"
      type: "timeseries"
      targets:
        - expr: "histogram_quantile(0.95, sum(rate(http_server_duration_ms_bucket[5m])) by (le, http_method))"
          legend: "{{http_method}}"
    
    - id: 3
      title: "Thread Pool Utilization"
      type: "timeseries"
      targets:
        - expr: "process_thread_count"
          name: "Thread Count"
        - expr: "process_max_threads"
          name: "Max Threads"
    
    - id: 4
      title: "GC Heap Size"
      type: "timeseries"
      targets:
        - expr: "dotnet_total_memory_bytes / 1024 / 1024"
          name: "Memory (MB)"
    
    - id: 5
      title: "Active Orders"
      type: "stat"
      targets:
        - expr: "orders_active"
          name: "Active Orders"
    
    - id: 6
      title: "Orders Created Rate"
      type: "timeseries"
      targets:
        - expr: "rate(orders_created_total[5m])"
          name: "Orders/sec"
```

### 6.2 Alerting Rules - Foundation Layer

```yaml
# Prometheus Alerting Rules - Foundation
groups:
  - name: foundation_alerts
    interval: 30s
    rules:
      - alert: HighErrorRate
        expr: |
          (sum(rate(http_server_duration_ms_count{http_status=~"5.."}[5m])) / 
           sum(rate(http_server_duration_ms_count[5m]))) > 0.05
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"
          description: "Error rate is {{ $value | humanizePercentage }} for the last 5 minutes"
          
      - alert: HighLatency
        expr: |
          histogram_quantile(0.95, sum(rate(http_server_duration_ms_bucket[5m])) by (le)) > 1000
        for: 5m
        labels:
          severity: warning
        annotations:
          summary: "High latency detected"
          description: "p95 latency is {{ $value }}ms"
          
      - alert: ThreadPoolExhaustion
        expr: process_thread_count > 4500
        for: 2m
        labels:
          severity: critical
        annotations:
          summary: "Thread pool near exhaustion"
          description: "Thread count is {{ $value }}"
          
      - alert: HighMemoryUsage
        expr: dotnet_total_memory_bytes > 8 * 1024 * 1024 * 1024
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "Memory usage exceeds 8GB"
          description: "Memory usage is {{ $value | humanize }}"
```

### 6.3 Distributed Tracing Architecture

```mermaid
```

![### 6.3 Distributed Tracing Architecture](images/diagram_08_63-distributed-tracing-architecture-4e74.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/diagram_08_63-distributed-tracing-architecture-4e74.md)


### 6.4 Logging Strategy

**Log Levels and Usage:**

![**Log Levels and Usage:**](images/table_14_log-levels-and-usage.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_14_log-levels-and-usage.md)


**Structured Log Format:**

```json
{
  "@timestamp": "2026-03-22T10:30:45.123Z",
  "level": "Information",
  "message": "Order created successfully",
  "application": "ECommerceAPI",
  "environment": "production",
  "correlationId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "userId": "user_12345",
  "orderId": "order_67890",
  "customerId": "cust_12345",
  "orderTotal": 299.99,
  "itemCount": 3,
  "durationMs": 245,
  "httpMethod": "POST",
  "httpPath": "/api/orders",
  "statusCode": 201,
  "traceId": "abc123def456"
}
```

---

## 7. Migration Strategy - Part 1

### 7.1 Phased Approach for Foundation Remediation

```mermaid
```

![### 7.1 Phased Approach for Foundation Remediation](images/diagram_09_71-phased-approach-for-foundation-remediation-c079.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/diagram_09_71-phased-approach-for-foundation-remediation-c079.md)


### 7.2 Risk Mitigation - Part 1

![### 7.2 Risk Mitigation - Part 1](images/table_15_72-risk-mitigation---part-1.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_15_72-risk-mitigation---part-1.md)


### 7.3 Rollback Strategy - Part 1

```csharp
// Feature flags for safe rollout of foundation changes
public static class FeatureFlags
{
    public const string UseMediatR = "FeatureManagement:UseMediatR";
    public const string UseGlobalExceptionHandler = "FeatureManagement:UseGlobalExceptionHandler";
    public const string UseAsyncDatabase = "FeatureManagement:UseAsyncDatabase";
    public const string UseObservability = "FeatureManagement:UseObservability";
}

// Controller with feature flag fallback
[ApiController]
[Route("api/[controller]")]
public class OrdersController : ControllerBase
{
    private readonly IMediator _mediator;
    private readonly IFeatureManager _featureManager;
    private readonly LegacyOrderService _legacyService;
    private readonly ILogger<OrdersController> _logger;
    
    [HttpPost]
    public async Task<IActionResult> CreateOrder(
        CreateOrderCommand command, 
        CancellationToken ct)
    {
        if (await _featureManager.IsEnabledAsync(FeatureFlags.UseMediatR))
        {
            _logger.LogInformation("Using MediatR handler for order creation");
            var result = await _mediator.Send(command, ct);
            return result.Match(Ok, Problem);
        }
        else
        {
            _logger.LogWarning("Falling back to legacy order service");
            var result = await _legacyService.CreateOrder(command, ct);
            return Ok(result);
        }
    }
}
```

### 7.4 Training & Adoption - Part 1

![### 7.4 Training & Adoption - Part 1](images/table_16_74-training--adoption---part-1.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_16_74-training--adoption---part-1.md)


---

## 8. Summary & Next Steps

### 8.1 Part 1 Accomplishments

![### 8.1 Part 1 Accomplishments](images/table_17_81-part-1-accomplishments.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_17_81-part-1-accomplishments.md)


### 8.2 Performance Improvements - Part 1

![### 8.2 Performance Improvements - Part 1](images/table_18_82-performance-improvements---part-1-dffb.png)

[View Source](https://github.com/Vineet-Sharma-Medium-Stories/Medium-Assets/blob/main/architectural-remediation-framework-eliminating-the-12-silent-killers-in-net-10-web-apis---part-1/table_18_82-performance-improvements---part-1-dffb.md)


### 8.3 Key Takeaways - Part 1

1. **Async is Not Optional**: Proper async/await with cancellation tokens is fundamental to scalability. Every async call must receive the cancellation token.

2. **Observability Must Be Built-In**: You cannot add observability after the fact. Logs, metrics, and traces must be part of the initial design.

3. **Fail Fast and Fail Clearly**: Validate inputs at the boundary and return consistent, actionable error responses using Problem Details.

4. **Separate HTTP from Business Logic**: Controllers should only handle HTTP concerns. Business logic belongs in handlers that can be tested independently.

5. **Thread Pool Starvation is Invisible**: You won't know your threads are blocked until production collapses under load. Use async/await exclusively.

6. **Cancellation Tokens are Free**: Adding cancellation token support costs almost nothing but prevents resource waste on abandoned requests.

### 8.4 The Story Continues

With the foundation of observability, async patterns, and exception handling in place, the e-commerce platform was stable. The thread pool was no longer exhausted. Errors were structured and traceable. But we still had a major problem: the data layer.

When we looked at the database logs, we saw queries returning 10,000 rows for a single API request. The checkout page was downloading 50MB of data. The database CPU was still at 95% despite the async improvements.

**The next morning**: We began Part 2 of the remediation—fixing data access patterns, pagination, over-fetching, and API contracts.

### 8.5 Next Steps - Continue to Part 2

The foundation established in Part 1 enables the next phase of remediation: fixing data access patterns and API contract issues.

**Continue to Part 2**: [Architectural Remediation Framework: Eliminating the 12 Silent Killers in .NET 10 Web APIs - Part 2](#)

*Part 2 will cover:*
- **Anti-Pattern #6: No Pagination** - Implementing efficient pagination for collection endpoints
- **Anti-Pattern #7: Wrong HTTP Status Codes** - Proper REST semantics
- **Anti-Pattern #8: Over-fetching Data** - Projections and DTOs
- **Anti-Pattern #9: Returning EF Entities** - Decoupling database from API contracts

---

## Appendix A: Code Analysis Tools - Part 1

```xml
<!-- .NET 10 Analyzers for Foundation Anti-Pattern Detection -->
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>net10.0</TargetFramework>
    <Nullable>enable</Nullable>
    <AnalysisMode>All</AnalysisMode>
    <EnableNETAnalyzers>true</EnableNETAnalyzers>
    <AnalysisLevel>latest</AnalysisLevel>
    <EnforceCodeStyleInBuild>true</EnforceCodeStyleInBuild>
  </PropertyGroup>

  <ItemGroup>
    <!-- Microsoft Code Analysis -->
    <PackageReference Include="Microsoft.CodeAnalysis.NetAnalyzers" Version="8.0.0">
      <PrivateAssets>all</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
    </PackageReference>
    
    <!-- SonarAnalyzer for C# -->
    <PackageReference Include="SonarAnalyzer.CSharp" Version="9.15.0">
      <PrivateAssets>all</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
    </PackageReference>
    
    <!-- AsyncFixer for detecting sync-over-async -->
    <PackageReference Include="AsyncFixer" Version="1.6.0">
      <PrivateAssets>all</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers</IncludeAssets>
    </PackageReference>
  </ItemGroup>
</Project>
```

---

*This document is Part 1 of a three-part architectural series. For questions or clarifications, contact: architecture@ecommerce.com*

**Continue to Part 2** →
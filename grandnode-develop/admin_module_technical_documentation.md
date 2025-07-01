# GrandNode Admin Module - Technical Documentation

## 1. Overview

The Admin Module in GrandNode is a comprehensive back-office management system that provides administrators with tools to manage all aspects of the e-commerce platform. It follows a structured MVC (Model-View-Controller) architecture within an ASP.NET Core framework, with clear separation of concerns and a service-oriented approach.

## 2. Technologies

### 2.1 Backend Technologies

#### Core Framework
- **ASP.NET Core 3.1**: The main web application framework
- **C#**: Primary programming language
- **MVC Pattern**: Architectural pattern for the web application

#### Database
- **MongoDB 2.11.5**: NoSQL document database used for data storage
- **MongoDB.Driver**: Official MongoDB C# driver
- **MongoDB.GridFS**: For storing and retrieving large files

#### Dependency Injection
- **Microsoft.Extensions.DependencyInjection**: Built-in DI container
- **Autofac**: Extended DI capabilities

#### Authentication & Authorization
- **ASP.NET Core Identity**: User management and authentication
- **JWT (JSON Web Tokens)**: For API authentication
- **Microsoft.AspNetCore.Authentication.JwtBearer**: JWT implementation
- **GoogleAuthenticator**: For two-factor authentication

#### Caching
- **Microsoft.Extensions.Caching.Memory**: In-memory caching
- **StackExchange.Redis**: Distributed caching with Redis

#### Logging & Monitoring
- **Serilog.AspNetCore**: Structured logging

#### Validation
- **FluentValidation**: For model validation
- **FluentValidation.AspNetCore**: ASP.NET Core integration for FluentValidation

#### Object Mapping
- **AutoMapper 10.1.1**: For object-to-object mapping

#### Templating
- **DotLiquid 2.0.366**: For email templates and other dynamic content
- **Razor**: For view rendering

#### PDF Generation
- **Rotativa/wkhtmltopdf**: For PDF generation from HTML

#### Messaging & Event Handling
- **MediatR 9.0.0**: For in-process messaging and CQRS pattern

#### Code Analysis
- **Microsoft.CodeAnalysis.CSharp**: For dynamic compilation
- **Microsoft.CodeAnalysis.CSharp.Scripting**: For script execution

### 2.2 Frontend Technologies

#### JavaScript Libraries
- **jQuery 3.3.1**: Core JavaScript library
- **jQuery Validate**: Client-side validation
- **jQuery Unobtrusive Validation**: ASP.NET integration for jQuery Validate
- **jQuery Countdown**: For countdown timers
- **jQuery Magnific Popup**: For lightbox functionality
- **jQuery Smooth Scroll**: For smooth scrolling
- **jQuery Zoom**: For image zoom functionality
- **jQuery Templates (tmpl)**: For client-side templating

#### CSS Frameworks
- **Bootstrap**: For responsive design
- **Bootstrap Treeview**: For hierarchical data display

#### Other Frontend Technologies
- **AJAX**: For asynchronous requests
- **Push Notifications**: For real-time notifications
- **Firebase Messaging**: For push notifications

### 2.3 DevOps & Deployment

- **Docker**: For containerized deployment
- **Docker Compose**: For multi-container Docker applications

### 2.4 Architecture Patterns

- **Domain-Driven Design (DDD)**: Evident in the project structure
- **CQRS (Command Query Responsibility Segregation)**: Through MediatR
- **Repository Pattern**: For data access abstraction
- **Service-Oriented Architecture**: For business logic organization
- **Plugin Architecture**: For extensibility

## 3. Architecture

### 3.1 Module Structure

The Admin module is organized as an Area within the ASP.NET Core MVC application structure. This approach allows for separation of the admin functionality from the storefront while still being part of the same application. The module follows a clean, hierarchical organization:

```
Grand.Web/Areas/Admin/
├── Components/                 # ViewComponents for reusable UI elements
│   ├── AdminWidget/           # Widget components for the admin dashboard
│   ├── CommonStatistics/      # Statistics display components
│   └── OrderStatistics/       # Order-related statistics components
├── Controllers/               # MVC Controllers handling admin requests (50+ controllers)
│   ├── BaseAdminController.cs # Base controller with common functionality
│   ├── ProductController.cs   # Product management (largest controller)
│   └── ...                    # Other domain-specific controllers
├── Extensions/                # Extension methods for admin functionality
│   ├── Mapping/               # AutoMapper profile extensions
│   └── ...                    # Other extension methods
├── Infrastructure/            # Core infrastructure setup
│   ├── DependencyRegistrar.cs # Service registration
│   ├── RouteProvider.cs       # Admin route configuration
│   ├── Mapper/                # AutoMapper configuration
│   └── ...                    # Other infrastructure components
├── Interfaces/                # Interface definitions for admin services
│   ├── IProductViewModelService.cs
│   ├── IOrderViewModelService.cs
│   └── ...                    # Other service interfaces
├── Models/                    # View models for admin views
│   ├── Catalog/               # Product-related models
│   ├── Customers/             # Customer-related models
│   ├── Orders/                # Order-related models
│   ├── Settings/              # Settings-related models
│   └── ...                    # Other domain-specific models (20+ subfolders)
├── Services/                  # Implementation of admin services
│   ├── ProductViewModelService.cs
│   ├── OrderViewModelService.cs
│   └── ...                    # Other service implementations
├── Validators/                # FluentValidation validators
│   ├── Catalog/               # Product-related validators
│   ├── Customers/             # Customer-related validators
│   └── ...                    # Other domain-specific validators
└── Views/                     # Razor views for the admin interface
    ├── _ViewImports.cshtml    # Common imports for all views
    ├── Shared/                # Shared layouts and partial views
    ├── Product/               # Product management views
    ├── Order/                 # Order management views
    └── ...                    # Other domain-specific views
```

### 3.2 Core Components

1. **BaseAdminController**: Abstract base controller that all admin controllers inherit from, providing common functionality such as:
   - Authorization and authentication checks
   - Anti-forgery token validation
   - IP address validation
   - Tab index management for multi-tab forms

2. **Service Layer**: Follows a service-oriented architecture with:
   - Interfaces defined in `Interfaces/` directory
   - Implementations in `Services/` directory
   - Registered via dependency injection in `DependencyRegistrar.cs`

3. **ViewModels**: Dedicated models for admin views, separate from domain entities

4. **Validators**: FluentValidation-based validators for admin models

## 4. Authentication and Authorization

### 4.1 Authentication System

GrandNode uses ASP.NET Core's built-in authentication system with the following key components:

1. **Cookie-based Authentication**: The primary authentication mechanism is cookie-based authentication, where user credentials are validated and an authentication cookie is issued.

2. **Custom Authentication Flow**:
   - Users log in through the `LoginController` in the admin area
   - Credentials are validated against the customer database
   - Upon successful validation, an authentication cookie is created
   - The authentication cookie contains claims about the user's identity and roles

3. **Work Context**: The `IWorkContext` interface provides access to the current customer (user) throughout the application, which is used for authentication checks.

### 4.2 Authorization System

GrandNode implements a multi-layered authorization system:

#### 4.2.1 Role-Based Authorization

The first layer is role-based authorization through the `AuthorizeAdmin` attribute:

```csharp
[ValidateIpAddress]
[AuthorizeAdmin]
[AutoValidateAntiforgeryToken]
[Area(Constants.AreaAdmin)]
[ValidateVendor]
public abstract partial class BaseAdminController : BaseController
```

The `AuthorizeAdmin` attribute checks if the current user has access to the admin panel by:
- Verifying the user is authenticated
- Checking if the user has the `StandardPermissionProvider.AccessAdminPanel` permission
- Redirecting to the login page if unauthorized

#### 4.2.2 Permission-Based Authorization

The second layer is a granular permission system implemented through the `PermissionAuthorize` attribute:

```csharp
[PermissionAuthorize(PermissionSystemName.CustomerRoles)]
public partial class CustomerRoleController : BaseAdminController
```

This attribute:
- Checks if the user has the specific permission required for the controller/action
- Redirects to "Access Denied" page if the user lacks the required permission
- Works by calling the `IPermissionService.Authorize()` method

#### 4.2.3 Permission Service

The core of the authorization system is the `PermissionService` which:

1. **Stores permissions** in the database as `PermissionRecord` entities
2. **Maps permissions to customer roles** through a many-to-many relationship
3. **Checks permissions** through the `Authorize()` method:
   ```csharp
   public virtual async Task<bool> Authorize(string permissionRecordSystemName, Customer customer)
   {
       if (String.IsNullOrEmpty(permissionRecordSystemName))
           return false;

       var customerRoles = customer.CustomerRoles.Where(cr => cr.Active);
       foreach (var role in customerRoles)
           if (await Authorize(permissionRecordSystemName, role))
               //yes, we have such permission
               return true;

       //no permission found
       return false;
   }
   ```

4. **Uses caching** to improve performance of permission checks:
   ```csharp
   protected virtual async Task<bool> Authorize(string permissionRecordSystemName, CustomerRole customerRole)
   {
       string key = string.Format(CacheKey.PERMISSIONS_ALLOWED_KEY, customerRole.Id, permissionRecordSystemName);
       return await _cacheBase.GetAsync(key, async () =>
       {
           var permissionRecord = await _permissionRecordRepository.Table.FirstOrDefaultAsync(x => x.SystemName == permissionRecordSystemName);
           return permissionRecord?.CustomerRoles.Contains(customerRole.Id) ?? false;
       });
   }
   ```

### 4.3 Additional Security Layers

1. **IP Address Validation** (`ValidateIpAddress` attribute):
   - Restricts admin access to specific IP addresses if configured
   - Provides an additional layer of security for admin access

2. **Vendor Validation** (`ValidateVendor` attribute):
   - Ensures vendors can only access their own data
   - Restricts vendor access to specific controllers and actions

3. **Anti-Forgery Token** (`AutoValidateAntiforgeryToken` attribute):
   - Prevents Cross-Site Request Forgery (CSRF) attacks
   - Validates that form submissions come from the same site

### 4.4 Permission Management

Permissions in GrandNode are:

1. **Defined** in the `StandardPermissionProvider` class
2. **Installed** during system installation
3. **Managed** through the admin interface
4. **Assigned** to customer roles
5. **Checked** at runtime through the permission service

## 5. Key Functionalities

### 5.1 Dashboard (HomeController)

The admin dashboard provides a quick overview of store performance:

- Pending orders count
- Abandoned shopping carts
- Low stock products
- Today's registered customers
- Return requests
- Google Analytics integration (when configured)

### 5.2 Catalog Management

- **ProductController**: Comprehensive product management (121KB, largest controller)
  - CRUD operations for products
  - Product variant management
  - Product attributes and specifications
  - Related products, cross-sells, and up-sells
  - Inventory management
  - Product reviews

- **CategoryController**: Category hierarchy management
- **ManufacturerController**: Brand management
- **ProductAttributeController**: Attribute management
- **SpecificationAttributeController**: Specification management

### 4.3 Order Management

- **OrderController**: Complete order processing (73KB)
  - Order creation and editing
  - Order status management
  - Payment processing
  - Refund handling
  - Order notes

- **ShipmentController**: Shipment tracking and management
- **ReturnRequestController**: Return merchandise authorization
- **GiftCardController**: Gift card management

### 5.4 Customer Management

- **CustomerController**: Customer account management (56KB)
  - Customer information
  - Address management
  - Order history
  - Activity log
  - Impersonation

- **CustomerRoleController**: Role-based access control
- **CustomerTagController**: Customer tagging for segmentation
- **CustomerActionController**: Automated customer actions

### 5.5 Marketing Tools

- **DiscountController**: Promotion management
- **CampaignController**: Email campaign management
- **NewsletterSubscriptionController**: Newsletter management
- **AffiliateController**: Affiliate program management
- **BlogController**: Blog post management
- **NewsController**: News management

### 5.6 Content Management

- **TopicController**: Static page management
- **KnowledgebaseController**: Knowledge base articles
- **PollController**: Customer polls
- **BannerController**: Banner management

### 5.7 Configuration

- **SettingController**: System configuration (167KB, second largest controller)
  - General settings
  - Customer settings
  - Catalog settings
  - Order settings
  - Tax settings
  - Shipping settings
  - Payment settings
  - Media settings
  - Security settings

- **StoreController**: Multi-store configuration
- **TaxController**: Tax calculation settings
- **ShippingController**: Shipping method configuration
- **PaymentController**: Payment method configuration

### 5.8 System

- **PluginController**: Plugin management
- **ScheduleTaskController**: Background task scheduling
- **LogController**: System log viewing
- **TemplateController**: Email and page templates
- **SecurityController**: Security settings

## 6. Service Layer

The admin module implements a service-oriented architecture with view model services that:

1. Map between domain entities and view models
2. Encapsulate business logic specific to the admin interface
3. Handle CRUD operations for admin views
4. Provide a clean separation between controllers and data access

Key services include:

- `IProductViewModelService`: Product management operations
- `IOrderViewModelService`: Order processing operations
- `ICustomerViewModelService`: Customer management operations
- `IDiscountViewModelService`: Discount and promotion operations

## 7. Dependency Injection

Services are registered in `DependencyRegistrar.cs` using the built-in ASP.NET Core dependency injection container:

```csharp
public virtual void Register(IServiceCollection serviceCollection, ITypeFinder typeFinder, GrandConfig config)
{
    serviceCollection.AddScoped<IProductViewModelService, ProductViewModelService>();
    serviceCollection.AddScoped<IOrderViewModelService, OrderViewModelService>();
    // Additional services...
}
```

## 8. Routing

Admin routes are configured in `RouteProvider.cs` with a dedicated area name:

```csharp
public void RegisterRoutes(IEndpointRouteBuilder routeBuilder)
{
    //admin index
    routeBuilder.MapControllerRoute("AdminIndex", $"admin/", 
        new { controller = "Home", action = "Index", area = Constants.AreaAdmin });

    //admin login
    routeBuilder.MapControllerRoute("AdminLogin", $"admin/login/", 
        new { controller = "Login", action = "Index", area = Constants.AreaAdmin });
}
```

## 9. View Components

The admin module uses view components for reusable UI elements, located in the `Components` directory. These components provide modular UI elements that can be reused across different admin pages.

## 10. Multi-Store Support

The admin interface supports managing multiple stores from a single admin panel:

1. Store-specific settings
2. Store-specific content
3. Store selection for viewing store-specific data
4. Permissions for managing specific stores

## 11. User Roles and Permissions

### 11.1 System-Defined Roles

GrandNode implements a role-based access control system with several predefined system roles:

1. **Administrators**
   - System Name: "Administrators"
   - Full access to all features of the admin panel
   - Can manage all aspects of the store including products, orders, customers, settings, and system configuration

2. **Staff**
   - System Name: "Staff"
   - General staff members with limited administrative permissions
   - Can be restricted to specific stores in a multi-store setup
   - Permissions are configurable by administrators

3. **Sales Manager**
   - System Name: "SalesManager"
   - Focused on sales operations with access to order management, customer information, and sales reports
   - Typically don't have access to technical system settings

4. **Vendors**
   - System Name: "Vendors"
   - External sellers with limited admin access
   - Can only manage their own products
   - Limited access to orders containing their products
   - Cannot access system configuration
   - Custom validation via `ValidateVendor` attribute

5. **Registered**
   - System Name: "Registered"
   - Regular registered customers
   - Not typically given admin access
   - Used to identify registered users versus guests

6. **Guests**
   - System Name: "Guests"
   - Unregistered visitors
   - Not given admin access
   - Used for tracking and permissions for anonymous users

### 11.2 Role Properties

Each customer role has the following properties:

- **Name**: Display name of the role
- **SystemName**: Internal identifier used by the system
- **IsSystemRole**: Indicates if it's a predefined system role
- **Active**: Whether the role is currently active
- **FreeShipping**: Whether users with this role get free shipping
- **TaxExempt**: Whether users with this role are exempt from taxes
- **EnablePasswordLifetime**: Whether password expiration is enabled for this role

### 11.3 Permission System

The admin module uses a granular permission system that works in conjunction with roles:

1. **Permission Definition**: Each admin feature has a corresponding permission defined in `PermissionSystemName` class

2. **Permission Assignment**: Permissions are assigned to roles through the admin interface

3. **Permission Validation**: The `PermissionAuthorize` attribute on controllers checks if the current user has the required permission:

```csharp
[PermissionAuthorize(PermissionSystemName.CustomerRoles)]
public partial class CustomerRoleController : BaseAdminController
{
    // Controller implementation
}
```

4. **Permission Inheritance**: Users inherit permissions from all roles they belong to

5. **Permission Checking**: The `IPermissionService` is used to programmatically check permissions

### 11.4 Custom Roles

Administrators can create custom roles with specific permissions to create granular access levels for different administrative staff members. This allows for precise control over which users can access specific parts of the admin module.

### 11.5 Vendor Management

Vendors have a specialized role in the system with restricted access to the admin panel:

1. Can only manage their own products
2. Limited access to orders containing their products
3. Cannot access system configuration
4. Custom validation via `ValidateVendor` attribute

## 11. Security Considerations

1. **Authentication**: Admin users must be authenticated
2. **Authorization**: Role-based access control for admin functions
3. **Permission-Based Access**: Granular permissions for specific features
4. **Anti-Forgery**: CSRF protection via tokens
5. **IP Restriction**: Optional IP address validation
6. **Activity Logging**: All admin actions are logged
7. **Password Policies**: Configurable password requirements and expiration
8. **Store Restrictions**: Staff can be limited to specific stores in multi-store setups

## 12. Integration Points

The admin module integrates with:

1. **Core Domain Services**: For business operations
2. **Event System**: For extensibility via events
3. **Plugin System**: For extending admin functionality
4. **External Services**: Payment gateways, shipping providers, etc.

## 13. Technical Debt and Considerations

1. Some controllers are quite large (ProductController: 121KB, SettingController: 167KB) and could benefit from further refactoring
2. The admin module uses a mix of synchronous and asynchronous operations
3. Validation is handled through a combination of data annotations and FluentValidation

## 14. Extension Points

The admin module can be extended through:

1. **Plugins**: Adding new admin menu items and functionality
2. **Events**: Subscribing to admin events
3. **Custom Services**: Implementing custom admin services
4. **Custom Controllers**: Adding new admin controllers

## 15. Performance Considerations

1. **Caching**: Admin views use caching for performance
2. **Pagination**: Large data sets are paginated
3. **Async Operations**: Long-running operations are handled asynchronously
4. **Deferred Loading**: Complex data is loaded on demand

## 16. API Documentation

The admin module exposes several API endpoints for programmatic interaction with the admin functionality:

### 16.1 Authentication

All API endpoints require authentication using JWT (JSON Web Tokens):

```
POST /api/token/create
Content-Type: application/json

{
  "Email": "admin@example.com",
  "Password": "password"
}
```

Response includes a token to be used in subsequent requests:

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expiration": "2023-01-01T00:00:00Z"
}
```

### 16.2 Common Endpoints

- **Products API**: `/api/admin/products`
  - GET: List products with filtering and pagination
  - POST: Create new product
  - PUT: Update existing product
  - DELETE: Remove product

- **Orders API**: `/api/admin/orders`
  - GET: List orders with filtering and pagination
  - PUT: Update order status

- **Customers API**: `/api/admin/customers`
  - GET: List customers with filtering and pagination
  - POST: Create new customer
  - PUT: Update customer details

### 16.3 Response Format

All API responses follow a consistent format:

```json
{
  "success": true,
  "message": "",
  "data": { ... },
  "errors": []
}
```

## 17. Localization Implementation

The admin module implements a comprehensive localization system:

### 17.1 Resource Files

Localization resources are stored in:
- XML files in the `App_Data/Localization` directory
- Each language has its own set of resource files
- Admin-specific resources are in separate files from storefront resources

### 17.2 Language Selection

- Admin users can select their preferred language from their profile
- Language selection is stored in user preferences
- Default language is determined by the store settings

### 17.3 Translation Implementation

```csharp
// In controllers
public async Task<IActionResult> List()
{
    var model = new ProductListModel();
    model.AvailableStores.Add(new SelectListItem() {
        Text = _localizationService.GetResource("Admin.Common.All"),
        Value = ""
    });
    return View(model);
}
```

### 17.4 Translation Workflow

1. New strings are added to the default language resource file
2. Translation tool in the admin panel allows for translation to other languages
3. Translations can be exported and imported via XML

## 18. Testing Strategy

### 18.1 Unit Testing

- Unit tests are implemented using xUnit
- Services and controllers have corresponding test classes
- Mocking framework is used to isolate components

```csharp
[Fact]
public async Task Can_Get_Product_List()
{
    // Arrange
    var productService = new Mock<IProductService>();
    var productViewModelService = new ProductViewModelService(productService.Object, ...);
    
    // Act
    var result = await productViewModelService.PrepareProductListModel(...);
    
    // Assert
    Assert.NotNull(result);
}
```

### 18.2 Integration Testing

- Integration tests verify interactions between components
- Test database is used with seeded test data
- Authentication and authorization are tested with different user roles

### 18.3 UI Testing

- Selenium is used for UI testing of critical admin workflows
- Test scripts cover common admin operations
- Visual regression testing ensures UI consistency

## 19. Deployment Guidelines

### 19.1 Environment Configuration

- Environment-specific settings are stored in `appsettings.{Environment}.json`
- Production environments should have caching enabled
- Sensitive settings should be stored in environment variables or secure storage

### 19.2 Deployment Process

1. Build the application in Release mode
2. Run database migrations if needed
3. Deploy to the target environment
4. Verify admin module functionality
5. Monitor for errors during initial usage

### 19.3 Scaling Considerations

- Admin module can be deployed separately from the storefront
- Session state should be configured for load-balanced environments
- Cache invalidation strategies should be implemented for multi-server setups

## 20. Troubleshooting Guide

### 20.1 Common Issues

1. **Authentication Failures**
   - Check user roles and permissions
   - Verify cookie settings and domain configuration
   - Check for IP restrictions if enabled

2. **Performance Problems**
   - Enable query caching
   - Check for missing indexes in the database
   - Review large data transfers in admin operations

3. **Plugin Integration Issues**
   - Verify plugin compatibility with the current version
   - Check for conflicts between plugins
   - Review plugin initialization order

### 20.2 Debugging Techniques

- Enable detailed error logging in `appsettings.json`
- Use browser developer tools to inspect network requests
- Check server logs for exceptions and warnings

## 21. Upgrade Procedures

### 21.1 Upgrade Process

1. Back up the existing installation
2. Review release notes for breaking changes
3. Update NuGet packages or replace files
4. Run database migrations
5. Clear cache and restart the application
6. Test critical admin functionality

### 21.2 Version Compatibility

- Admin module version should match the core GrandNode version
- Custom plugins may need updates for compatibility
- Theme customizations should be reviewed after upgrades

## 22. Visual Elements and UI Framework

### 22.1 Admin Theme

The admin interface uses:
- Bootstrap 4 as the base CSS framework
- Custom admin theme with responsive design
- Font Awesome icons for visual elements

### 22.2 UI Components

- Data tables with sorting, filtering, and pagination
- Modal dialogs for quick edits and confirmations
- Form validation with client and server-side validation
- Rich text editors for content management
- File upload with preview functionality

### 22.3 JavaScript Libraries

- jQuery for DOM manipulation
- jQuery Validate for form validation
- Select2 for enhanced dropdown controls
- Chart.js for data visualization
- Bootstrap components for UI elements

## 23. Data Flow Diagrams

### 23.1 Product Creation Flow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Admin UI   │────▶│  Controller │────▶│ View Model  │
└─────────────┘     └─────────────┘     └─────────────┘
                           │                   │
                           ▼                   ▼
                     ┌─────────────┐     ┌─────────────┐
                     │   Service   │────▶│  Validator  │
                     └─────────────┘     └─────────────┘
                           │
                           ▼
                     ┌─────────────┐     ┌─────────────┐
                     │ Repository  │────▶│  Database   │
                     └─────────────┘     └─────────────┘
```

### 23.2 Order Processing Flow

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Order     │────▶│  Order      │────▶│ Inventory   │
│   Update    │     │  Service    │     │ Update      │
└─────────────┘     └─────────────┘     └─────────────┘
                           │
                           ▼
                     ┌─────────────┐     ┌─────────────┐
                     │ Notification│────▶│  Customer   │
                     │   Service   │     │  Email      │
                     └─────────────┘     └─────────────┘
```

## 24. Monitoring and Logging

### 24.1 Logging Implementation

The admin module uses Serilog for structured logging:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddSerilog(dispose: true);
    });
}
```

### 24.2 Log Categories

- **Information**: Normal operations and user actions
- **Warning**: Potential issues that don't affect functionality
- **Error**: Exceptions and failures
- **Critical**: System-wide failures requiring immediate attention

### 24.3 Audit Logging

Admin actions are logged with the following information:
- User ID and name
- Action performed
- Affected entity
- Timestamp
- IP address

### 24.4 Monitoring

- Health checks are available at `/admin/system/health`
- Performance metrics can be exported to monitoring systems
- Resource usage is tracked and displayed in the admin dashboard

## 25. Glossary of Terms

| Term | Definition |
|------|------------|
| **ACL** | Access Control List, used for permission management |
| **CQRS** | Command Query Responsibility Segregation, a pattern used in GrandNode |
| **DDD** | Domain-Driven Design, the architectural approach used |
| **GridFS** | MongoDB's system for storing large files |
| **IWorkContext** | Interface providing access to the current customer context |
| **MediatR** | Library used for implementing the mediator pattern and CQRS |
| **PermissionRecord** | Entity representing a single permission in the system |
| **SystemName** | Internal identifier used for entities like roles and permissions |
| **ValidateVendor** | Attribute used to restrict vendor access to their own resources |
| **ViewModelService** | Service layer between controllers and domain services |

## 26. Conclusion

The GrandNode Admin Module provides a comprehensive management interface for the e-commerce platform. It follows modern architectural patterns with clear separation of concerns, dependency injection, and service-oriented design. The module is designed to be extensible, secure, and performant, allowing for effective management of all aspects of the e-commerce system.

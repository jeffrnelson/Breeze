# Breeze System Architecture

Comprehensive architectural overview of the AI-Powered Product Listing System.

## Table of Contents
- [System Overview](#system-overview)
- [Architecture Patterns](#architecture-patterns)
- [Component Design](#component-design)
- [Data Flow](#data-flow)
- [Technology Stack](#technology-stack)
- [Security Architecture](#security-architecture)
- [Scalability Considerations](#scalability-considerations)
- [Deployment Architecture](#deployment-architecture)

## System Overview

Breeze is a distributed system designed to streamline the process of creating product listings for multiple sales platforms through AI-powered analysis and automation.

### Design Principles

1. **Separation of Concerns**: Clear boundaries between mobile, API, and data layers
2. **Clean Architecture**: Domain-driven design with infrastructure abstraction
3. **API-First**: RESTful API as the central integration point
4. **Cross-Platform**: Mobile app runs on iOS, Android, Windows, and macOS
5. **Testability**: Dependency injection and interfaces throughout
6. **Resilience**: Retry policies, error handling, and graceful degradation

## Architecture Patterns

### Overall Architecture: N-Tier + CQRS

```
┌────────────────────────────────────────────────────────────────┐
│                        Presentation Layer                       │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │          .NET MAUI Mobile App (iOS/Android/Win)         │  │
│  │  ┌──────────┐  ┌────────────┐  ┌────────────┐          │  │
│  │  │  Views   │  │ ViewModels │  │  Services  │          │  │
│  │  │  (XAML)  │  │   (MVVM)   │  │ (API Client)│          │  │
│  │  └──────────┘  └────────────┘  └────────────┘          │  │
│  └───────────────────────┬──────────────────────────────────┘  │
└────────────────────────────┼────────────────────────────────────┘
                             │ HTTPS/JSON
                             ↓
┌────────────────────────────────────────────────────────────────┐
│                      Application Layer (API)                    │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              ASP.NET Core Web API                       │  │
│  │  ┌────────────┐  ┌──────────┐  ┌────────────┐          │  │
│  │  │Controllers │  │   DTOs   │  │ Middleware │          │  │
│  │  └──────┬─────┘  └──────────┘  └────────────┘          │  │
│  └─────────┼────────────────────────────────────────────────┘  │
│            ↓                                                    │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │              Business Logic Layer                       │  │
│  │  ┌────────────┐  ┌──────────┐  ┌────────────┐          │  │
│  │  │  Services  │  │  Domain  │  │Interfaces  │          │  │
│  │  │  (Logic)   │  │  Models  │  │ (Contracts)│          │  │
│  │  └────────────┘  └──────────┘  └────────────┘          │  │
│  └─────────┬────────────────────────────────────────────────┘  │
└────────────┼─────────────────────────────────────────────────┘
             ↓
┌────────────────────────────────────────────────────────────────┐
│                    Infrastructure Layer                         │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  ┌────────────┐  ┌──────────┐  ┌────────────┐          │  │
│  │  │     EF     │  │ External │  │   File     │          │  │
│  │  │   Core     │  │   APIs   │  │  Storage   │          │  │
│  │  └──────┬─────┘  └────┬─────┘  └─────┬──────┘          │  │
│  └─────────┼─────────────┼──────────────┼─────────────────┘  │
└────────────┼─────────────┼──────────────┼──────────────────────┘
             ↓             ↓              ↓
     ┌─────────────┐  ┌────────┐   ┌──────────┐
     │ SQL Server  │  │   AI   │   │  Azure   │
     │  Database   │  │ APIs   │   │  Blob    │
     └─────────────┘  └────────┘   └──────────┘
```

### Mobile App: MVVM Pattern

```
┌──────────────────────────────────────────────────────┐
│                      View (XAML)                     │
│  ┌────────────────────────────────────────────────┐  │
│  │  UI Elements, Data Binding, User Interactions  │  │
│  └────────────────────┬───────────────────────────┘  │
└───────────────────────┼──────────────────────────────┘
                        │ Data Binding
                        │ Commands
                        ↓
┌──────────────────────────────────────────────────────┐
│                   ViewModel                          │
│  ┌────────────────────────────────────────────────┐  │
│  │  Properties, Commands, Business Logic          │  │
│  │  Observable Collections, State Management      │  │
│  └────────────┬───────────────────┬───────────────┘  │
└───────────────┼───────────────────┼──────────────────┘
                │                   │
                ↓                   ↓
┌─────────────────────┐    ┌───────────────────┐
│       Models        │    │     Services      │
│  ┌───────────────┐  │    │  ┌─────────────┐  │
│  │ Domain Entities│  │    │  │ API Client  │  │
│  │ DTOs           │  │    │  │ Local DB    │  │
│  └───────────────┘  │    │  └─────────────┘  │
└─────────────────────┘    └───────────────────┘
```

### Backend API: Clean Architecture

```
┌──────────────────────────────────────────────────────────┐
│                      API Layer                           │
│  ┌────────────────────────────────────────────────────┐  │
│  │  Controllers                                       │  │
│  │  • ProductsController                             │  │
│  │  • Handles HTTP requests                          │  │
│  │  • Maps to DTOs                                   │  │
│  └───────────────────┬────────────────────────────────┘  │
└──────────────────────┼───────────────────────────────────┘
                       ↓
┌──────────────────────────────────────────────────────────┐
│                   Core/Domain Layer                      │
│  ┌────────────────────────────────────────────────────┐  │
│  │  Domain Models                                     │  │
│  │  • Product, ProductImage, Defect, PricingBands    │  │
│  │  • Business Rules                                 │  │
│  │  • Interfaces (IProductRepository, etc.)          │  │
│  └───────────────────┬────────────────────────────────┘  │
└──────────────────────┼───────────────────────────────────┘
                       ↓
┌──────────────────────────────────────────────────────────┐
│              Infrastructure Layer                        │
│  ┌────────────────────────────────────────────────────┐  │
│  │  ApplicationDbContext (EF Core)                    │  │
│  │  • Product, ProductImages, Defects tables         │  │
│  │  • Migrations                                     │  │
│  │  • Repository Implementations                     │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────┘
                       ↓
┌──────────────────────────────────────────────────────────┐
│                  Services Layer                          │
│  ┌────────────────────────────────────────────────────┐  │
│  │  Business Logic Services                          │  │
│  │  • AI Analysis Service (planned)                  │  │
│  │  • Image Processing Service (planned)             │  │
│  │  • Pricing Service (planned)                      │  │
│  └────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────┘
```

## Component Design

### Mobile App Components

#### Views
- **MainPage** - Home/welcome screen
- **CameraPage** - Multi-photo capture with guided workflow
- **ResultsPage** - AI analysis results with editing capabilities

#### ViewModels
- **CameraViewModel**
  - Photo capture management
  - Multi-photo handling (add, delete, reorder)
  - API submission orchestration
  - Loading states and error handling

- **ResultsViewModel**
  - Display AI analysis results
  - Edit functionality (title, description, pricing)
  - Pricing band selection (Red/Yellow/Green)
  - Multi-platform publishing selection
  - Save draft and publish actions

#### Services
- **ApiService**
  - HTTP client with Polly retry policies
  - Handles all API communication
  - Methods:
    - `AnalyzeProductAsync()` - Send images for AI analysis
    - `GetProductsAsync()` - Retrieve user products
    - `UpdateProductAsync()` - Update product details
    - `PublishProductAsync()` - Publish to platforms (planned)

#### Models
- **Product** - Complete product entity
- **ProductImage** - Image metadata and analysis
- **Defect** - AI-detected defect details
- **PricingBands** - Three-tier pricing structure
- **ProductAnalysisResult** - API response model

### Backend API Components

#### Controllers
- **ProductsController**
  - `POST /api/products/analyze` - Analyze product with images
  - `GET /api/products?userId={guid}` - List user products
  - `GET /api/products/{id}` - Get specific product
  - `PUT /api/products/{id}` - Update product
  - `DELETE /api/products/{id}` - Delete product

#### Domain Models
- **Product**
  ```csharp
  - ProductId (Guid, PK)
  - UserId (Guid)
  - Brand, Category, ConditionGrade
  - Title, Description
  - PricingBands (Red/Yellow/Green)
  - PublishStatus, PublishPlatforms
  - CreatedAt, UpdatedAt
  ```

- **ProductImage**
  ```csharp
  - ImageId (Guid, PK)
  - ProductId (Guid, FK)
  - ImageUrl, ThumbnailUrl
  - ImageType, ImageOrder
  - AnalysisMetadata (JSON)
  ```

- **Defect**
  ```csharp
  - DefectId (Guid, PK)
  - ProductId (Guid, FK)
  - ImageId (Guid, FK)
  - DefectType, Severity
  - Location, Size, Confidence
  - PricePenalty
  ```

#### Database Context
- **ApplicationDbContext**
  - EF Core DbContext
  - Configures entity relationships
  - Automatic timestamp management
  - Cascade delete configuration

### Database Schema

```sql
Products
├── ProductId (PK, UNIQUEIDENTIFIER)
├── UserId (UNIQUEIDENTIFIER, Indexed)
├── Brand (NVARCHAR(100))
├── Category (NVARCHAR(100))
├── ConditionGrade (NVARCHAR(50))
├── Title (NVARCHAR(200))
├── Description (NVARCHAR(MAX))
├── RedBandPrice (DECIMAL(18,2))
├── YellowBandPrice (DECIMAL(18,2))
├── GreenBandPrice (DECIMAL(18,2))
├── SelectedBand (NVARCHAR(10))
├── PublishStatus (NVARCHAR(50), Indexed)
├── PublishPlatforms (NVARCHAR(MAX))
├── CreatedAt (DATETIME2, Indexed)
└── UpdatedAt (DATETIME2)

ProductImages
├── ImageId (PK, UNIQUEIDENTIFIER)
├── ProductId (FK → Products.ProductId, CASCADE DELETE)
├── ImageUrl (NVARCHAR(500))
├── ThumbnailUrl (NVARCHAR(500))
├── ImageType (NVARCHAR(50))
├── ImageOrder (INT, Indexed)
├── AnalysisMetadata (NVARCHAR(MAX), JSON)
└── CreatedAt (DATETIME2)

Defects
├── DefectId (PK, UNIQUEIDENTIFIER)
├── ProductId (FK → Products.ProductId, CASCADE DELETE)
├── ImageId (FK → ProductImages.ImageId, CASCADE DELETE)
├── DefectType (NVARCHAR(100))
├── Severity (NVARCHAR(50), Indexed)
├── Location (NVARCHAR(200))
├── Size (NVARCHAR(100))
├── Confidence (DECIMAL(5,4))
├── PricePenalty (DECIMAL(18,2))
└── CreatedAt (DATETIME2)
```

## Data Flow

### User Workflow: Product Listing Creation

```
1. Photo Capture (Mobile)
   ┌─────────────────┐
   │  User opens     │
   │  CameraPage     │
   └────────┬────────┘
            │
            ↓
   ┌─────────────────┐
   │  Capture/select │
   │  multiple photos│
   │  (5+ angles)    │
   └────────┬────────┘
            │
            ↓
   ┌─────────────────┐
   │  Review photos, │
   │  tap Analyze    │
   └────────┬────────┘
            │
            ↓

2. API Request (Mobile → API)
   ┌─────────────────────────────────┐
   │ ApiService.AnalyzeProductAsync()│
   │ • Convert images to byte arrays │
   │ • Create multipart form data    │
   │ • Send POST to /api/products/   │
   │   analyze                       │
   │ • Apply retry policy (Polly)    │
   └────────┬────────────────────────┘
            │ HTTP POST
            ↓

3. Backend Processing (API)
   ┌─────────────────────────────────┐
   │ ProductsController.Analyze()    │
   │ • Validate request              │
   │ • Create Product entity         │
   │ • Save to database              │
   │ • [Planned] Call AI service     │
   │ • [Planned] Process images      │
   │ • [Planned] Calculate pricing   │
   │ • Return ProductAnalysisResult  │
   └────────┬────────────────────────┘
            │
            ↓

4. Database Persistence (EF Core)
   ┌─────────────────────────────────┐
   │ ApplicationDbContext            │
   │ • Save Product                  │
   │ • Save ProductImages (1:many)   │
   │ • Save Defects (1:many)         │
   │ • Set timestamps                │
   │ • Apply transaction             │
   └────────┬────────────────────────┘
            │
            ↓

5. Response Handling (Mobile)
   ┌─────────────────────────────────┐
   │ Navigate to ResultsPage         │
   │ • Display analyzed product      │
   │ • Show image carousel           │
   │ • Display AI-generated title    │
   │ • Display description           │
   │ • Show defects detected         │
   │ • Display pricing bands         │
   │ • Enable editing                │
   └────────┬────────────────────────┘
            │
            ↓

6. User Review & Edit
   ┌─────────────────────────────────┐
   │ User edits:                     │
   │ • Title                         │
   │ • Description                   │
   │ • Select pricing band           │
   │ • Select publish platforms      │
   └────────┬────────────────────────┘
            │
            ↓

7. Publishing (Planned)
   ┌─────────────────────────────────┐
   │ Tap Publish                     │
   │ • Update product via API        │
   │ • Publish to Shopify            │
   │ • Publish to eBay               │
   │ • Publish to Facebook           │
   │ • Update PublishStatus          │
   └─────────────────────────────────┘
```

## Technology Stack

### Mobile Application
```
.NET MAUI App
├── Framework: .NET 10.0
├── UI: XAML with CommunityToolkit.Maui v13.0.0
├── MVVM: CommunityToolkit.Mvvm v8.4.0
├── HTTP: HttpClient + Polly v8.6.5
├── Local Storage: SQLite (sqlite-net-pcl v1.9.172)
├── JSON: Newtonsoft.Json v13.0.4
└── Platforms:
    ├── iOS 15.0+
    ├── Android API 21+
    ├── Windows 10 19041.0+
    └── macOS Catalyst 26.0+
```

### Backend API
```
ASP.NET Core API
├── Framework: .NET 10.0
├── Language: C# 13
├── ORM: Entity Framework Core 10.0.1
├── Database: SQL Server (LocalDB dev, SQL Server prod)
├── API Docs: Swashbuckle.AspNetCore 10.1.0
├── Logging: Serilog.AspNetCore 10.0.0
├── Auth: JWT (Microsoft.AspNetCore.Authentication.JwtBearer 10.0.1)
└── AI Integration (Planned):
    ├── Claude API
    └── OpenAI API
```

### Database
```
SQL Server Database Project
├── Type: .sqlproj (SSDT)
├── Deployment: DACPAC via SqlPackage
├── CI/CD: GitHub Actions
├── Environments:
    ├── Development (LocalDB)
    ├── Staging (SQL Server)
    └── Production (SQL Server with backup)
```

## Security Architecture

### Current Security Measures

1. **HTTPS Enforcement**
   - All API communication encrypted via TLS
   - Certificate validation

2. **CORS Configuration**
   - Restricted to specific origins
   - Configured per environment

3. **Input Validation**
   - DTO validation
   - Model state validation
   - Parameter sanitization

4. **Database Security**
   - Parameterized queries (EF Core)
   - SQL injection prevention
   - Connection string encryption

### Planned Security Features

1. **Authentication & Authorization**
   - JWT token-based auth
   - Role-based access control (RBAC)
   - OAuth 2.0 integration

2. **Data Protection**
   - At-rest encryption for sensitive data
   - Personal data anonymization
   - GDPR compliance measures

3. **API Security**
   - Rate limiting
   - API key management
   - Request throttling

4. **Image Security**
   - Secure upload/download URLs (SAS tokens)
   - Malware scanning
   - Content moderation

## Scalability Considerations

### Current Optimizations

1. **Database Performance**
   - Indexed columns: UserId, CreatedAt, PublishStatus, ImageOrder, Severity
   - Decimal precision (18,2) for pricing
   - Cascade delete for efficient cleanup

2. **API Performance**
   - EF Core retry policies (5 retries, 30s max)
   - Asynchronous operations throughout
   - Structured logging for monitoring

3. **Mobile App Performance**
   - Polly retry policies (3 attempts)
   - Async/await patterns
   - Image caching
   - MVVM separation for testability

### Future Scalability

1. **Horizontal Scaling**
   - Stateless API design for load balancing
   - Distributed caching (Redis)
   - CDN for image delivery
   - Message queue for async processing

2. **Microservices Migration** (if needed)
   - AI Analysis Service
   - Image Processing Service
   - Publishing Service
   - Pricing Service

3. **Database Scaling**
   - Read replicas
   - Partitioning by UserId
   - Archive old data
   - Sharding strategy

4. **Performance Monitoring**
   - Application Insights
   - Performance metrics
   - Error tracking
   - User analytics

## Deployment Architecture

### Development Environment
```
Developer Machine
├── SQL Server LocalDB
├── Visual Studio 2022
├── .NET 10 SDK
├── iOS Simulator / Android Emulator
└── Git
```

### Staging Environment
```
Azure/AWS Cloud
├── Web App Service (API)
│   ├── .NET 10 runtime
│   ├── Auto-scaling (2-4 instances)
│   └── Logging to Application Insights
├── SQL Server Database (Staging)
│   ├── Standard tier
│   ├── Automated backups
│   └── Point-in-time restore
└── Blob Storage (Images - Staging)
    ├── Standard performance
    └── LRS redundancy
```

### Production Environment
```
Azure/AWS Cloud
├── Web App Service (API)
│   ├── .NET 10 runtime
│   ├── Auto-scaling (3-10 instances)
│   ├── Load balancer
│   └── SSL/TLS certificates
├── SQL Server Database (Production)
│   ├── Premium tier
│   ├── Geo-redundant backups
│   ├── Point-in-time restore (35 days)
│   └── Automated patching
├── Blob Storage (Images)
│   ├── Premium performance
│   ├── GRS redundancy
│   └── CDN integration
└── Application Insights
    ├── Performance monitoring
    ├── Error tracking
    ├── User analytics
    └── Custom dashboards
```

### CI/CD Pipeline

```
┌─────────────┐
│   GitHub    │
│  Repository │
└──────┬──────┘
       │ Push to dev/main
       ↓
┌──────────────────────┐
│  GitHub Actions      │
│  ┌────────────────┐  │
│  │  Build & Test  │  │
│  │  • Restore     │  │
│  │  • Build       │  │
│  │  • Test        │  │
│  │  • Code coverage│ │
│  └────────┬───────┘  │
└───────────┼──────────┘
            ↓
┌───────────────────────┐
│  Database Deployment  │
│  • Build DACPAC       │
│  • Deploy to Dev      │
│  • (Approval gate)    │
│  • Deploy to Staging  │
│  • (Approval gate)    │
│  • Backup Production  │
│  • Deploy to Prod     │
└───────────┬───────────┘
            │
            ↓
┌───────────────────────┐
│  API Deployment       │
│  • Publish Release    │
│  • Deploy to Azure    │
│  • Run smoke tests    │
│  • Health check       │
└───────────────────────┘
```

## Integration Points

### External Services (Planned)

1. **AI Services**
   - Claude API (Anthropic)
   - OpenAI GPT-4 Vision
   - Azure Computer Vision

2. **Sales Platforms**
   - Shopify API
   - eBay API
   - Facebook Marketplace API
   - Instagram Shopping API

3. **Cloud Storage**
   - Azure Blob Storage
   - AWS S3 (alternative)

4. **Monitoring & Analytics**
   - Application Insights
   - Google Analytics
   - Sentry (error tracking)

## Design Decisions

### Why .NET MAUI?
- Single codebase for iOS, Android, Windows, macOS
- Native performance
- MVVM pattern support
- Strong ecosystem and tooling
- Corporate backing (Microsoft)

### Why Clean Architecture?
- Testability
- Maintainability
- Separation of concerns
- Platform independence
- Easy to extend

### Why SQL Server?
- Enterprise-grade reliability
- Strong tooling (SSMS, SSDT)
- DACPAC deployments
- Familiar to .NET developers
- Excellent EF Core support

### Why Entity Framework Core?
- Code-first migrations
- LINQ queries
- Automatic change tracking
- Transaction support
- Connection resiliency

## Future Architecture Considerations

1. **Event-Driven Architecture**
   - Event bus for loose coupling
   - Domain events
   - CQRS pattern

2. **GraphQL API**
   - Alternative to REST
   - Efficient data fetching
   - Real-time subscriptions

3. **Serverless Functions**
   - Image processing
   - Background jobs
   - Scheduled tasks

4. **Machine Learning Integration**
   - On-device ML (ML.NET)
   - Custom models for pricing
   - Continuous learning from sales data

---

**Document Version**: 1.0
**Last Updated**: January 2026
**Maintained By**: Development Team

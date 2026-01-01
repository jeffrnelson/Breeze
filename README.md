# Breeze - AI-Powered Product Listing System

Complete end-to-end solution for streamlining product listings across multiple sales platforms using AI-powered analysis.

## Overview

Breeze is a unified system combining:
- **Mobile Application** (.NET MAUI) - Multi-platform app for capturing and managing product photos
- **Backend API** (ASP.NET Core) - REST API for AI analysis and business logic
- **Database** (SQL Server) - Persistent data storage with automated deployment

**Goal**: Enable users to photograph used items and create professional multi-platform listings in approximately 30 seconds.

## Project Structure

This is a unified solution combining three integrated repositories:

```
Breeze/
├── AI-Product-Listing-API/         # Backend REST API
│   ├── src/                        # Source code
│   │   ├── AIProductListing.API/           # Web API controllers & endpoints
│   │   ├── AIProductListing.Core/          # Domain models & interfaces
│   │   ├── AIProductListing.Infrastructure/# Database & EF Core
│   │   └── AIProductListing.Services/      # Business logic & AI services
│   ├── tests/                      # Unit & integration tests
│   ├── README.md                   # API documentation
│   └── SETUP.md                    # API setup guide
│
├── AI-Product-Listing-Mobile/      # Cross-platform mobile app
│   └── src/
│       └── AIProductListing.Mobile/
│           ├── Views/              # XAML pages
│           ├── ViewModels/         # MVVM view models
│           ├── Services/           # API client & services
│           ├── Models/             # Data models
│           └── Platforms/          # Platform-specific code
│
├── AI-Product-Listing-DB/          # Database project
│   └── AIProductListing.Database/
│       ├── Tables/                 # SQL table definitions
│       ├── Scripts/                # Pre/Post deployment scripts
│       └── *.sqlproj               # SQL Server Database Project
│
└── Breeze/                         # Master solution
    └── Breeze.slnx                 # Unified solution file
```

## Technology Stack

### Backend API
- **Framework**: ASP.NET Core 10.0
- **Language**: C# 13
- **Database**: SQL Server with Entity Framework Core 10.0
- **AI Integration**: Planned (Claude Sonnet 4 / OpenAI)
- **API Documentation**: Swagger/OpenAPI
- **Authentication**: JWT (Planned)

### Mobile Application
- **Framework**: .NET MAUI (.NET 10.0)
- **Language**: C# 12
- **Platforms**: iOS 15.0+, Android API 21+, Windows 10 19041.0+
- **Architecture**: MVVM with CommunityToolkit
- **Local Storage**: SQLite
- **HTTP Client**: HttpClient with Polly retry logic

### Database
- **Type**: SQL Server Database Project (.sqlproj)
- **Deployment**: DACPAC-based with GitHub Actions CI/CD
- **Environments**: Development, Staging, Production

## Current Status (January 2026)

### ✅ Phase 1: Foundation - COMPLETE
- Backend API with full CRUD operations
- Database schema with automated deployments
- Mobile app UI/UX foundation
- MVVM architecture implementation
- API integration with retry logic

### 🔄 Phase 2: AI Integration - IN PROGRESS
- AI-powered product analysis
- Image processing and storage
- Intelligent pricing calculation
- Automated description generation

### ⏳ Phase 3: Platform Publishing - PLANNED
- Shopify integration
- eBay API integration
- Facebook Marketplace
- Instagram Shopping
- Multi-platform publishing workflow

### ⏳ Phase 4: Advanced Features - PLANNED
- Offline mode with sync
- Push notifications
- Analytics dashboard
- Batch processing
- Continuous learning from sales data

## Quick Start

### Prerequisites
- [.NET 10.0 SDK](https://dotnet.microsoft.com/download/dotnet/10.0)
- [Visual Studio 2022](https://visualstudio.microsoft.com/) or [VS Code](https://code.visualstudio.com/)
- [SQL Server LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb) (for development)
- [Git](https://git-scm.com/)

### Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/YOUR_USERNAME/Breeze.git
   cd Breeze
   ```

2. **Open the master solution**
   - Open `Breeze/Breeze.slnx` in Visual Studio 2022
   - Or work with individual solution files in each repository

3. **Set up the Backend API**
   ```bash
   cd AI-Product-Listing-API
   dotnet restore
   dotnet ef database update --project src/AIProductListing.Infrastructure
   cd src/AIProductListing.API
   dotnet run
   ```
   API will be available at `https://localhost:5217`

4. **Set up the Mobile App**
   ```bash
   cd AI-Product-Listing-Mobile/src/AIProductListing.Mobile
   dotnet restore
   dotnet build -f net10.0-android  # For Android
   # Or
   dotnet build -f net10.0-ios      # For iOS (macOS only)
   ```

5. **Deploy the Database** (Optional - API migrations handle this)
   ```bash
   cd AI-Product-Listing-DB/AIProductListing.Database
   msbuild AIProductListing.Database.sqlproj /p:Configuration=Release
   sqlpackage /Action:Publish /SourceFile:"bin\Release\AIProductListing.Database.dacpac" /TargetConnectionString:"YOUR_CONNECTION_STRING"
   ```

For detailed setup instructions, see:
- [API Setup Guide](./AI-Product-Listing-API/SETUP.md)
- [Mobile Setup Guide](./AI-Product-Listing-Mobile/SETUP.md)
- [Database Deployment Guide](./AI-Product-Listing-DB/DEPLOYMENT_GUIDE.md)

## Architecture

### System Design

```
┌─────────────┐
│   Mobile    │
│     App     │  ←→  Camera, Photos, UI
│  (MAUI)     │
└──────┬──────┘
       │ HTTPS/REST
       ↓
┌─────────────┐
│  Backend    │
│     API     │  ←→  AI Services (Claude/OpenAI)
│ (ASP.NET)   │
└──────┬──────┘
       │ EF Core
       ↓
┌─────────────┐
│  SQL Server │
│  Database   │
└─────────────┘
       │
       ↓
┌─────────────┐
│  Publishing │  ←→  Shopify, eBay, Facebook, Instagram
│  Platforms  │
└─────────────┘
```

### Key Features

**Mobile App:**
- 📸 Multi-angle photo capture with guided workflow
- 🎯 Real-time photo management (add, delete, reorder)
- 👁️ Results preview with AI analysis
- ✏️ Edit title, description, and pricing
- 💰 Three-tier pricing selection (Red/Yellow/Green bands)
- 🚀 Multi-platform publishing selection

**Backend API:**
- RESTful endpoints for product CRUD operations
- AI analysis integration (planned)
- Image processing and storage (planned)
- Pricing engine (planned)
- Multi-channel publishing (planned)

**Database:**
- Schema-as-code with SQL Server Database Project
- Automated CI/CD deployments
- Environment-specific configurations
- Automatic backups for production deployments

## Development Workflow

### Branches
- `main` - Stable, production-ready code
- `dev` - Active development branch
- `feature/*` - Feature branches

### Recommended Development Flow
1. Create feature branch from `dev`
2. Develop and test locally
3. Run tests: `dotnet test`
4. Create pull request to `dev`
5. After review, merge to `dev`
6. Periodically merge `dev` to `main` for releases

### Testing
```bash
# Run all tests
dotnet test

# Run API tests only
cd AI-Product-Listing-API
dotnet test

# Run with coverage
dotnet test /p:CollectCoverage=true
```

## Documentation

- **[Architecture Guide](./ARCHITECTURE.md)** - System architecture and design decisions
- **[API Documentation](./AI-Product-Listing-API/README.md)** - Backend API details
- **[Mobile Documentation](./AI-Product-Listing-Mobile/README.md)** - Mobile app details
- **[Database Documentation](./AI-Product-Listing-DB/README.md)** - Database schema and deployment
- **[Contributing Guidelines](./CONTRIBUTING.md)** - How to contribute
- **[Changelog](./AI-Product-Listing-API/CHANGELOG.md)** - Version history

## Platform Support

### Mobile App
| Platform | Minimum Version | Status |
|----------|----------------|--------|
| iOS | 15.0+ | ✅ Supported |
| Android | 5.0 (API 21+) | ✅ Supported |
| Windows | 10 (19041.0+) | ✅ Supported |
| macOS | 12.0+ | ✅ Supported |

### Backend API
- ✅ Windows Server 2016+
- ✅ Linux (Ubuntu 20.04+, Debian, etc.)
- ✅ macOS (for development)
- ✅ Docker containers

## API Endpoints

### Products
- `POST /api/products/analyze` - Analyze product images
- `GET /api/products?userId={guid}` - List user products
- `GET /api/products/{id}` - Get product details
- `PUT /api/products/{id}` - Update product
- `DELETE /api/products/{id}` - Delete product

### Health & Monitoring
- `GET /health` - Health check endpoint

For complete API documentation, visit `/swagger` when running the API locally.

## Configuration

### Backend API (`appsettings.json`)
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=AIProductListingDb;..."
  },
  "Cors": {
    "AllowedOrigins": ["http://localhost:3000"]
  }
}
```

### Mobile App (`appsettings.json`)
```json
{
  "API": {
    "BaseUrl": "https://localhost:5217",
    "Timeout": 30,
    "RetryAttempts": 3
  },
  "Features": {
    "EnableAIDescriptions": true,
    "EnablePricingSuggestions": true
  }
}
```

## Deployment

### Development
- Backend API runs on LocalDB
- Mobile app connects to local API
- Manual database deployments

### Staging/Production
- Automated database deployments via GitHub Actions
- Environment-specific configurations
- Automatic backups before production changes
- Health monitoring and logging

See [Deployment Guide](./AI-Product-Listing-DB/DEPLOYMENT_GUIDE.md) for details.

## Contributing

We welcome contributions! Please see our [Contributing Guidelines](./CONTRIBUTING.md) for details on:
- Code of Conduct
- Development setup
- Pull request process
- Coding standards
- Testing requirements

## License

This project is licensed under the MIT License - see the [LICENSE](./LICENSE) file for details.

## Support

- **Issues**: [GitHub Issues](https://github.com/YOUR_USERNAME/Breeze/issues)
- **Discussions**: [GitHub Discussions](https://github.com/YOUR_USERNAME/Breeze/discussions)
- **Email**: support@yourproject.com

## Roadmap

### Q1 2026
- ✅ Complete Phase 1: Foundation
- 🔄 Phase 2: AI Integration
- ⏳ Claude Sonnet 4 integration
- ⏳ Image processing pipeline

### Q2 2026
- ⏳ Shopify integration
- ⏳ eBay API integration
- ⏳ Beta testing program

### Q3 2026
- ⏳ Facebook Marketplace integration
- ⏳ Instagram Shopping integration
- ⏳ Public release

### Q4 2026
- ⏳ Analytics dashboard
- ⏳ Advanced features (offline mode, batch processing)
- ⏳ Continuous learning improvements

## Acknowledgments

- .NET Team for .NET MAUI and ASP.NET Core
- Community Toolkit contributors
- Claude AI for development assistance

## Project Status

For detailed project status and next steps, see:
- [API Project Status](./AI-Product-Listing-API/PROJECT_STATUS_AND_NEXT_STEPS.md)
- [CHANGELOG](./AI-Product-Listing-API/CHANGELOG.md)

---

**Built with ❤️ using .NET and AI**

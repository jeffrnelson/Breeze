# Contributing to Breeze

Thank you for your interest in contributing to the Breeze AI-Powered Product Listing System! This document provides guidelines and instructions for contributing.

## Table of Contents
- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [How to Contribute](#how-to-contribute)
- [Coding Standards](#coding-standards)
- [Testing Guidelines](#testing-guidelines)
- [Pull Request Process](#pull-request-process)
- [Issue Reporting](#issue-reporting)
- [Documentation](#documentation)
- [Community](#community)

## Code of Conduct

### Our Pledge

We pledge to make participation in our project a harassment-free experience for everyone, regardless of age, body size, disability, ethnicity, gender identity and expression, level of experience, nationality, personal appearance, race, religion, or sexual identity and orientation.

### Our Standards

**Positive behavior includes:**
- Using welcoming and inclusive language
- Being respectful of differing viewpoints
- Gracefully accepting constructive criticism
- Focusing on what is best for the community
- Showing empathy towards other community members

**Unacceptable behavior includes:**
- Trolling, insulting/derogatory comments, and personal or political attacks
- Public or private harassment
- Publishing others' private information without permission
- Other conduct which could reasonably be considered inappropriate

### Enforcement

Instances of abusive, harassing, or otherwise unacceptable behavior may be reported by contacting the project team. All complaints will be reviewed and investigated promptly and fairly.

## Getting Started

### Prerequisites

Before you begin, ensure you have:
- **Git** installed and configured
- **.NET 10.0 SDK** or later
- **Visual Studio 2022** or **VS Code** with C# extension
- **SQL Server LocalDB** (or SQL Server)
- **Basic knowledge** of C#, .NET MAUI, and ASP.NET Core

### Fork and Clone

1. **Fork the repository** on GitHub
2. **Clone your fork** locally:
   ```bash
   git clone https://github.com/YOUR_USERNAME/Breeze.git
   cd Breeze
   ```
3. **Add upstream remote**:
   ```bash
   git remote add upstream https://github.com/ORIGINAL_OWNER/Breeze.git
   ```

### Branch Strategy

We use Git Flow:
- `main` - Stable, production-ready code
- `dev` - Active development branch
- `feature/*` - New features
- `bugfix/*` - Bug fixes
- `hotfix/*` - Critical production fixes
- `release/*` - Release preparation

## Development Setup

### Backend API Setup

1. **Navigate to API directory**:
   ```bash
   cd AI-Product-Listing-API
   ```

2. **Restore packages**:
   ```bash
   dotnet restore
   ```

3. **Apply database migrations**:
   ```bash
   cd src/AIProductListing.API
   dotnet ef database update --project ../AIProductListing.Infrastructure
   ```

4. **Run the API**:
   ```bash
   dotnet run
   ```
   API should be available at `https://localhost:5217`

5. **Verify setup**:
   - Open browser to `https://localhost:5217/swagger`
   - Try `GET /health` endpoint

### Mobile App Setup

1. **Navigate to mobile directory**:
   ```bash
   cd AI-Product-Listing-Mobile/src/AIProductListing.Mobile
   ```

2. **Restore packages**:
   ```bash
   dotnet restore
   ```

3. **Update API URL** in `appsettings.json`:
   ```json
   {
     "API": {
       "BaseUrl": "https://localhost:5217"
     }
   }
   ```

4. **Run on Android** (example):
   ```bash
   dotnet build -t:Run -f net10.0-android
   ```

### Database Setup

1. **Navigate to database directory**:
   ```bash
   cd AI-Product-Listing-DB/AIProductListing.Database
   ```

2. **Build the project**:
   ```bash
   msbuild AIProductListing.Database.sqlproj /p:Configuration=Release
   ```

3. **Deploy locally** (optional - API migrations handle this):
   ```bash
   sqlpackage /Action:Publish /SourceFile:"bin\Release\AIProductListing.Database.dacpac" /TargetConnectionString:"Server=(localdb)\mssqllocaldb;Database=AIProductListingDb;Integrated Security=True"
   ```

## How to Contribute

### Types of Contributions

We welcome all types of contributions:
- 🐛 **Bug fixes** - Fix issues in existing code
- ✨ **New features** - Add new functionality
- 📝 **Documentation** - Improve or add documentation
- 🧪 **Tests** - Add or improve test coverage
- 🎨 **UI/UX** - Improve user interface and experience
- ⚡ **Performance** - Optimize existing code
- ♿ **Accessibility** - Improve accessibility features

### Finding Something to Work On

1. **Browse open issues**: Look for issues labeled `good first issue` or `help wanted`
2. **Check the project board**: See planned features and improvements
3. **Propose new features**: Open an issue to discuss before implementing

### Making Changes

1. **Create a feature branch** from `dev`:
   ```bash
   git checkout dev
   git pull upstream dev
   git checkout -b feature/your-feature-name
   ```

2. **Make your changes**:
   - Write clean, readable code
   - Follow coding standards (see below)
   - Add tests for new functionality
   - Update documentation as needed

3. **Commit your changes**:
   ```bash
   git add .
   git commit -m "Add feature: brief description"
   ```

4. **Keep your branch updated**:
   ```bash
   git fetch upstream
   git rebase upstream/dev
   ```

5. **Push to your fork**:
   ```bash
   git push origin feature/your-feature-name
   ```

6. **Open a Pull Request** on GitHub

## Coding Standards

### C# Code Style

Follow the official [C# Coding Conventions](https://docs.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions):

```csharp
// ✅ Good
public class ProductService : IProductService
{
    private readonly IProductRepository _repository;
    private readonly ILogger<ProductService> _logger;

    public ProductService(
        IProductRepository repository,
        ILogger<ProductService> logger)
    {
        _repository = repository ?? throw new ArgumentNullException(nameof(repository));
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));
    }

    public async Task<Product?> GetProductAsync(Guid id)
    {
        try
        {
            return await _repository.GetByIdAsync(id);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error retrieving product {ProductId}", id);
            throw;
        }
    }
}
```

**Key principles:**
- Use **PascalCase** for class names, method names, public properties
- Use **camelCase** for local variables, parameters
- Prefix private fields with `_`
- Use **meaningful names** - no abbreviations
- One class per file
- Maximum 300 lines per class (guideline)
- Maximum 50 lines per method (guideline)

### XAML Style

```xaml
<!-- ✅ Good -->
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:vm="clr-namespace:AIProductListing.Mobile.ViewModels"
             x:Class="AIProductListing.Mobile.Views.CameraPage"
             Title="Capture Photos">

    <ContentPage.BindingContext>
        <vm:CameraViewModel />
    </ContentPage.BindingContext>

    <Grid RowDefinitions="Auto,*,Auto">
        <!-- Header -->
        <Label Grid.Row="0"
               Text="Take Photos"
               Style="{StaticResource HeaderLabelStyle}" />

        <!-- Content -->
        <ScrollView Grid.Row="1">
            <!-- Content here -->
        </ScrollView>

        <!-- Actions -->
        <Button Grid.Row="2"
                Text="Analyze"
                Command="{Binding AnalyzeCommand}" />
    </Grid>
</ContentPage>
```

**XAML principles:**
- Proper indentation (4 spaces)
- One attribute per line for readability (when multiple attributes)
- Use styles and resources
- Bind to ViewModels, never code-behind
- Use meaningful x:Name values

### API Design

Follow REST best practices:

```csharp
// ✅ Good - RESTful endpoints
[HttpGet]
public async Task<ActionResult<IEnumerable<ProductDto>>> GetProducts([FromQuery] Guid userId)
{
    // Implementation
}

[HttpGet("{id}")]
public async Task<ActionResult<ProductDto>> GetProduct(Guid id)
{
    // Implementation
}

[HttpPost]
public async Task<ActionResult<ProductDto>> CreateProduct(CreateProductRequest request)
{
    // Implementation
}

[HttpPut("{id}")]
public async Task<ActionResult<ProductDto>> UpdateProduct(Guid id, UpdateProductRequest request)
{
    // Implementation
}

[HttpDelete("{id}")]
public async Task<IActionResult> DeleteProduct(Guid id)
{
    // Implementation
}
```

**API principles:**
- Use proper HTTP verbs (GET, POST, PUT, DELETE)
- Return appropriate status codes (200, 201, 400, 404, 500)
- Use DTOs for request/response (never expose domain models directly)
- Version your API (`/api/v1/...`)
- Document with XML comments for Swagger

### Database Conventions

- Table names: Plural (Products, ProductImages, Defects)
- Column names: PascalCase
- Primary keys: `[EntityName]Id` (e.g., ProductId)
- Foreign keys: `[ReferencedEntity]Id` (e.g., ProductId in ProductImages table)
- DateTime columns: Use DATETIME2
- Money columns: Use DECIMAL(18,2)
- Always include CreatedAt, UpdatedAt timestamps

## Testing Guidelines

### Unit Tests

Write unit tests for:
- Business logic
- ViewModels
- Services
- Controllers

**Example**:
```csharp
public class ProductServiceTests
{
    private readonly Mock<IProductRepository> _mockRepository;
    private readonly Mock<ILogger<ProductService>> _mockLogger;
    private readonly ProductService _service;

    public ProductServiceTests()
    {
        _mockRepository = new Mock<IProductRepository>();
        _mockLogger = new Mock<ILogger<ProductService>>();
        _service = new ProductService(_mockRepository.Object, _mockLogger.Object);
    }

    [Fact]
    public async Task GetProductAsync_WithValidId_ReturnsProduct()
    {
        // Arrange
        var productId = Guid.NewGuid();
        var expectedProduct = new Product { ProductId = productId, Title = "Test Product" };
        _mockRepository.Setup(r => r.GetByIdAsync(productId)).ReturnsAsync(expectedProduct);

        // Act
        var result = await _service.GetProductAsync(productId);

        // Assert
        Assert.NotNull(result);
        Assert.Equal(productId, result.ProductId);
        _mockRepository.Verify(r => r.GetByIdAsync(productId), Times.Once);
    }

    [Fact]
    public async Task GetProductAsync_WithInvalidId_ReturnsNull()
    {
        // Arrange
        var productId = Guid.NewGuid();
        _mockRepository.Setup(r => r.GetByIdAsync(productId)).ReturnsAsync((Product?)null);

        // Act
        var result = await _service.GetProductAsync(productId);

        // Assert
        Assert.Null(result);
    }
}
```

### Integration Tests

Test API endpoints end-to-end:

```csharp
public class ProductsControllerIntegrationTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly HttpClient _client;

    public ProductsControllerIntegrationTests(WebApplicationFactory<Program> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetProducts_ReturnsSuccessStatusCode()
    {
        // Act
        var response = await _client.GetAsync("/api/products?userId=00000000-0000-0000-0000-000000000001");

        // Assert
        response.EnsureSuccessStatusCode();
        var content = await response.Content.ReadAsStringAsync();
        Assert.NotNull(content);
    }
}
```

### Running Tests

```bash
# Run all tests
dotnet test

# Run with coverage
dotnet test /p:CollectCoverage=true

# Run specific test class
dotnet test --filter "FullyQualifiedName~ProductServiceTests"

# Run tests in watch mode
dotnet watch test
```

**Testing requirements**:
- **Minimum 70% code coverage** for new code
- All public methods should have tests
- Test edge cases and error conditions
- Use descriptive test names: `MethodName_Scenario_ExpectedResult`

## Pull Request Process

### Before Submitting

1. **Ensure all tests pass**:
   ```bash
   dotnet test
   ```

2. **Check code builds without warnings**:
   ```bash
   dotnet build --no-incremental
   ```

3. **Update documentation** if needed

4. **Review your changes**:
   ```bash
   git diff dev...your-branch
   ```

### PR Checklist

- [ ] Code follows project coding standards
- [ ] All tests pass
- [ ] New tests added for new functionality
- [ ] Documentation updated (README, API docs, etc.)
- [ ] Commit messages are clear and descriptive
- [ ] PR description explains what and why
- [ ] No merge conflicts with `dev` branch
- [ ] Screenshots included (for UI changes)

### PR Template

When creating a PR, include:

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Related Issue
Closes #123

## Changes Made
- Change 1
- Change 2
- Change 3

## Testing
- Tested on Android emulator
- Tested on iOS simulator
- All unit tests pass
- Manual testing performed

## Screenshots (if applicable)
[Add screenshots here]

## Checklist
- [ ] Code follows coding standards
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] Self-review completed
```

### Review Process

1. **Automated checks** run on PR creation
2. **Code review** by at least one maintainer
3. **Feedback addressed** by PR author
4. **Approval** from maintainer
5. **Merge** to `dev` branch

## Issue Reporting

### Bug Reports

When reporting bugs, include:

**Title**: Brief, descriptive title
**Environment**:
- OS: Windows 11 / macOS 14 / Android 13 / iOS 17
- App version: 1.0.0
- .NET version: 10.0.x

**Steps to Reproduce**:
1. Step one
2. Step two
3. Step three

**Expected Behavior**: What should happen
**Actual Behavior**: What actually happens
**Screenshots**: If applicable
**Logs**: Relevant error messages or logs

### Feature Requests

When requesting features, include:

**Title**: Brief, descriptive title
**Problem**: What problem does this solve?
**Proposed Solution**: How should this work?
**Alternatives Considered**: Other approaches considered
**Additional Context**: Any other relevant information

## Documentation

### Documentation Types

1. **Code Comments**
   - Use XML comments for public APIs
   - Explain "why" not "what" in inline comments
   - Keep comments up to date

2. **README Files**
   - Keep README.md files current
   - Include examples where helpful
   - Update setup instructions as needed

3. **API Documentation**
   - Document all public endpoints
   - Include request/response examples
   - Document error responses

4. **Architecture Docs**
   - Update ARCHITECTURE.md for significant changes
   - Document design decisions
   - Explain trade-offs

### Documentation Style

- Use clear, concise language
- Include code examples
- Use proper markdown formatting
- Keep it up to date with code changes

## Community

### Communication Channels

- **GitHub Issues**: Bug reports, feature requests
- **GitHub Discussions**: General questions, ideas
- **Pull Requests**: Code review, technical discussions

### Getting Help

If you need help:
1. Check existing documentation (README, ARCHITECTURE.md, etc.)
2. Search existing issues and discussions
3. Ask in GitHub Discussions
4. Contact maintainers

### Recognition

Contributors are recognized in:
- README.md contributors section
- Release notes
- Git commit history

Thank you for contributing to Breeze!

## License

By contributing, you agree that your contributions will be licensed under the same license as the project (MIT License).

---

**Questions?** Open a GitHub Discussion or contact the maintainers.
**Found a bug?** Open an issue with details.
**Have an idea?** We'd love to hear it - open a feature request!

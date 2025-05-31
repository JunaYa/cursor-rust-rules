# 🦀 Cursor Rust Rules

> **Production-tested Rust coding standards and best practices for Cursor IDE**

A comprehensive collection of Rust development rules designed for use with Cursor IDE, providing opinionated guidelines for building production-ready Rust applications with modern patterns and industry best practices.

## ✨ Features

- 🏗️ **Modern Architecture Patterns** - Domain-driven design, subsystem organization, and clean architecture
- 🌐 **Web Development Standards** - Axum web framework, OpenAPI integration, authentication patterns
- 🗄️ **Database Best Practices** - SQLx patterns, migrations, connection pooling, and repository patterns
- ⚡ **Async & Concurrency** - Tokio patterns, async/await best practices, and concurrent data structures
- 🛠️ **Utility Libraries** - JWT authentication, CLI development, HTTP clients, and more
- 📦 **Workspace Organization** - Multi-crate workspace structure with clear subsystem boundaries
- 🔍 **Code Quality** - Comprehensive linting, error handling, testing, and documentation standards

## 🚀 Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/your-username/cursor-rust-rules.git
cd cursor-rust-rules
```

### 2. Set Up Cursor Rules

Copy the rules to your Cursor configuration:

```bash
# Create cursor rules directory if it doesn't exist
mkdir -p ~/.cursor/rules/rust

# Copy all rules
cp -r .cursor/rules/rust/* ~/.cursor/rules/rust/
```

### 3. Use in Your Rust Project

In your Rust project, create or update `.cursorrules`:

```yaml
# .cursorrules
rules:
  - "@rust/main"          # Core Rust standards
  - "@rust/axum"          # Web development with Axum
  - "@rust/database"      # Database patterns with SQLx
  - "@rust/cli"           # CLI applications with clap
  - "@rust/concurrency"   # Async and concurrency patterns
  - "@rust/utilities"     # Utility libraries and CLI tools
  - "@rust/workspace"     # Multi-crate workspace organization
```

Or for specific features only:

```yaml
# .cursorrules - Minimal web API setup
rules:
  - "@rust/core/code-quality"
  - "@rust/features/axum"
  - "@rust/features/database"
```

## 📁 Project Structure

```
cursor-rust-rules/
├── .cursor/rules/rust/          # Cursor rule definitions
│   ├── main.mdc                 # Main rule loader and feature detection
│   ├── core/                    # Core standards
│   │   └── code-quality.mdc     # Code quality and best practices
│   ├── features/                # Feature-specific rules
│   │   ├── axum.mdc            # Web framework standards
│   │   ├── database.mdc        # Database patterns
│   │   ├── cli.mdc             # CLI application patterns
│   │   ├── concurrency.mdc     # Async/concurrency patterns
│   │   ├── utilities.mdc       # Utility libraries
│   │   ├── http-client.mdc     # HTTP client patterns
│   │   └── tools-and-config.mdc # Development tools
│   └── complex/                 # Advanced patterns
│       └── workspace.mdc        # Multi-crate workspace organization
└── README.md                    # This file
```

## 🎯 Rule Categories

### Core Standards

- **Code Quality** (`@rust/core/code-quality`) - Error handling, logging, testing, and code organization

### Feature-Specific Rules

- **Web Framework** (`@rust/features/axum`) - Axum 0.8+, OpenAPI, middleware, authentication
- **Database** (`@rust/features/database`) - SQLx patterns, migrations, connection pooling
- **CLI Applications** (`@rust/features/cli`) - Clap 4.0+, subcommands, enum_dispatch, modern CLI patterns
- **Concurrency** (`@rust/features/concurrency`) - Tokio, async patterns, concurrent data structures
- **Utilities** (`@rust/features/utilities`) - JWT, CLI tools, builder patterns, validation
- **HTTP Client** (`@rust/features/http-client`) - Reqwest patterns, retry logic, authentication
- **Tools & Config** (`@rust/features/tools-and-config`) - Development tooling and configuration

### Advanced Patterns

- **Workspace** (`@rust/complex/workspace`) - Subsystem-based multi-crate organization

## 🛠️ Usage Examples

### Web API Development

For building a web API with Axum:

```rust
// Cargo.toml gets configured with these dependencies
[dependencies]
axum = { version = "0.8", features = ["macros", "multipart", "ws"] }
utoipa = { version = "5.0", features = ["axum_extras", "chrono", "uuid"] }
sqlx = { version = "0.8", features = ["runtime-tokio-rustls", "postgres"] }
tokio = { version = "1.45", features = ["macros", "rt-multi-thread"] }
```

Your project follows patterns like:

```rust
// AppState with hot-reloadable config
#[derive(Clone)]
pub struct AppState {
    pub config: Arc<ArcSwap<AppConfig>>,
    pub db: PgPool,
    pub http_client: reqwest::Client,
}

// Route handlers with OpenAPI documentation
#[utoipa::path(
    post,
    path = "/api/v1/users",
    request_body = CreateUserRequest,
    responses(
        (status = 201, description = "User created", body = User),
        (status = 400, description = "Validation error", body = ApiError)
    ),
    tag = "users"
)]
pub async fn create_user(
    State(state): State<AppState>,
    Json(request): Json<CreateUserRequest>,
) -> Result<(StatusCode, Json<User>), ApiError> {
    // Implementation follows validation and error handling patterns
}
```

### Database Integration

For database operations with SQLx:

```rust
// Repository patterns with proper error handling
#[async_trait]
pub trait UserRepository: Send + Sync {
    async fn find_by_id(&self, id: UserId) -> Result<Option<User>, DatabaseError>;
    async fn create(&self, user: CreateUserRequest) -> Result<User, DatabaseError>;
    async fn update(&self, id: UserId, user: UpdateUserRequest) -> Result<User, DatabaseError>;
}

// Migration and connection management
pub async fn setup_database(config: &DatabaseConfig) -> Result<PgPool, DatabaseError> {
    let pool = PgPoolOptions::new()
        .max_connections(config.max_connections)
        .connect(&config.url)
        .await?;

    sqlx::migrate!("./migrations").run(&pool).await?;
    Ok(pool)
}
```

### CLI Application Development

For building powerful command-line tools:

```rust
// Cargo.toml gets configured with these dependencies
[dependencies]
clap = { version = "4.0", features = ["derive", "env", "unicode"] }
enum_dispatch = "0.3"
tokio = { version = "1.45", features = ["macros", "rt-multi-thread"] }
anyhow = "1.0"
colored = "2.0"
```

Your CLI follows the enum_dispatch pattern:

```rust
/// Command execution trait with enum_dispatch
#[async_trait]
#[enum_dispatch(Commands)]
pub trait CommandExecutor {
    async fn execute(&self, args: &Args, config: &Config) -> Result<()>;
}

/// All available commands
#[derive(Debug, Subcommand)]
#[enum_dispatch(CommandExecutor)]
pub enum Commands {
    /// Database management commands
    #[command(name = "db", alias = "database")]
    Database(DatabaseCommand),

    /// Server management commands
    #[command(name = "server", alias = "srv")]
    Server(ServerCommand),
}

// Each command implements CommandExecutor
#[async_trait]
impl CommandExecutor for DatabaseCommand {
    async fn execute(&self, args: &Args, config: &Config) -> Result<()> {
        // Command implementation with progress bars, colored output, etc.
    }
}
```

### Multi-Crate Workspace

For complex applications, organize as subsystems:

```
my-ecommerce-platform/
├── shared/                      # Cross-cutting infrastructure
│   ├── shared-types/           # Domain primitives
│   ├── shared-db/              # Database infrastructure
│   └── shared-events/          # Event definitions
├── subsystems/                 # Business subsystems
│   ├── user-management/        # User & auth subsystem
│   ├── product-catalog/        # Product management
│   ├── order-management/       # Order processing
│   └── payment/                # Payment processing
└── services/                   # Infrastructure services
    ├── api-gateway/            # Unified API
    └── admin-cli/              # Admin tools
```

## 🎮 Interactive Features

The rules provide intelligent feature detection and suggestions:

### Automatic Dependency Detection

When you use certain patterns, Cursor will suggest appropriate dependencies:

```rust
// Using async patterns triggers Tokio suggestions
async fn my_function() -> Result<(), Error> {
    // Cursor suggests: Add tokio = "1.45" with appropriate features
}

// Using database patterns triggers SQLx suggestions
sqlx::query!("SELECT * FROM users")
    // Cursor suggests: Add sqlx with postgres features
```

### Smart Code Generation

Rules include templates for common patterns:

- **API Endpoints** - Complete handler with validation and OpenAPI docs
- **Database Repositories** - Repository trait and implementation
- **Error Types** - Structured error handling with proper HTTP status codes
- **Configuration** - Environment-based config with hot reload support

## 📊 Rule Hierarchy

Rules are loaded based on project complexity:

1. **Simple Projects** - Core quality standards only
2. **Web APIs** - Core + Axum + Database rules
3. **Complex Systems** - All rules including workspace organization

## 🔧 Customization

### Override Specific Rules

Create a local `.cursorrules` file to customize:

```yaml
rules:
  - "@rust/main"

overrides:
  # Use different web framework
  web_framework: "warp"

  # Custom database configuration
  database:
    orm: "diesel"
    features: ["mysql"]
```

### Add Project-Specific Rules

Extend with your own patterns:

```rust
// .cursor/rules/rust/project/custom.mdc
// Your project-specific patterns and standards
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-rule`)
3. Add your rule with documentation and examples
4. Test with real projects
5. Submit a pull request

### Rule Writing Guidelines

- **Comprehensive Examples** - Show complete, working code
- **Production Patterns** - Based on real-world usage
- **Clear Documentation** - Explain why, not just what
- **Test Coverage** - Include testing patterns
- **Version Compatibility** - Specify crate versions

## 📚 References

- [Axum Documentation](https://docs.rs/axum/)
- [SQLx Documentation](https://docs.rs/sqlx/)
- [Tokio Documentation](https://docs.rs/tokio/)
- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)

## 📄 License

MIT License - see LICENSE file for details.

## 🚨 Disclaimer

These rules represent opinionated best practices based on production experience. Adapt them to your specific project needs and constraints.

---

**Happy Coding with Rust! 🦀**

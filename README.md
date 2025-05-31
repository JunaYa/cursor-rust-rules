# 🦀 Cursor Rust Rules

> **Production-tested Rust coding standards and best practices for Cursor IDE**

A comprehensive collection of Rust development rules designed for use with Cursor IDE, providing opinionated guidelines for building production-ready Rust applications with modern patterns and industry best practices.

## ✨ Features

- 🏗️ **Modern Architecture Patterns** - Domain-driven design, service-oriented architecture, and clean architecture
- 🌐 **Web Development Standards** - Axum web framework, OpenAPI integration, authentication patterns
- 🗄️ **Database Best Practices** - SQLx patterns, migrations, connection pooling, and repository patterns
- ⚡ **Async & Concurrency** - Tokio patterns, async/await best practices, and concurrent data structures
- ⚙️ **Configuration Management** - Multi-format config support, hot-reloading, validation, and environment overrides
- 📊 **Observability & Monitoring** - Metrics collection, distributed tracing, health checks, and performance monitoring
- 🛠️ **Utility Libraries** - JWT authentication, CLI development, HTTP clients, and more
- 📦 **Workspace Organization** - Multi-crate workspace structure with clear service boundaries
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
  - "@rust/configuration" # Configuration management patterns
  - "@rust/observability" # Metrics, tracing, and health checks
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
  - "@rust/features/observability"  # For production monitoring

# .cursorrules - Service with dynamic configuration
rules:
  - "@rust/core/code-quality"
  - "@rust/features/configuration"
  - "@rust/features/observability"
  - "@rust/features/utilities"
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
│   │   ├── configuration.mdc   # Configuration management patterns
│   │   ├── observability.mdc   # Metrics, tracing, health checks
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
- **Configuration** (`@rust/features/configuration`) - Multi-format config, hot-reloading, validation patterns
- **Observability** (`@rust/features/observability`) - Metrics collection, distributed tracing, health checks
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

# Configuration management
figment = { version = "0.10", features = ["yaml", "toml", "env"] }
arc-swap = "1.0"
validator = { version = "0.18", features = ["derive"] }

# Observability
prometheus = "0.13"
opentelemetry = "0.23"
tracing-opentelemetry = "0.23"
dashmap = "6.0"
```

Your project follows patterns like:

```rust
// AppState with hot-reloadable config and observability
#[derive(Clone)]
pub struct AppState {
    pub config: Arc<ArcSwap<AppConfig>>,
    pub db: PgPool,
    pub http_client: reqwest::Client,
    pub metrics: Arc<MetricsCollector>,
    pub health_manager: Arc<HealthManager>,
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

### Configuration Management

For dynamic configuration with hot-reloading:

```rust
// Multi-format configuration with validation
#[derive(Debug, Clone, Deserialize, Validate)]
pub struct AppConfig {
    #[validate(length(min = 1, max = 100))]
    pub name: String,

    #[validate(range(min = 1, max = 65535))]
    pub port: u16,

    #[validate(nested)]
    pub database: DatabaseConfig,

    #[serde(default)]
    #[validate(nested)]
    pub observability: ObservabilityConfig,
}

// Hot-reloadable configuration manager
pub struct ConfigManager<T> {
    current: Arc<ArcSwap<T>>,
    reload_tx: broadcast::Sender<Arc<T>>,
}

impl<T> ConfigManager<T>
where
    T: for<'de> Deserialize<'de> + Validate + Clone + Send + Sync + 'static,
{
    pub async fn start_watching(&self) -> Result<(), ConfigError> {
        // File system watching with atomic configuration updates
        // Supports YAML, TOML, JSON with environment variable overrides
    }
}
```

### Observability & Monitoring

For production-ready metrics and health checks:

```rust
// Lock-free metrics collection
pub struct MetricsCollector {
    counters: DashMap<String, Arc<AtomicCounter>>,
    prometheus_registry: Registry,
}

// Request timing middleware
pub struct RequestTimer {
    start_time: Instant,
    metrics: Arc<MetricsCollector>,
    operation: String,
}

// Health check framework
#[async_trait]
pub trait HealthCheck: Send + Sync {
    async fn check(&self) -> HealthCheckResult;
    fn name(&self) -> &str;
}

// Distributed tracing setup
pub fn setup_tracing(config: TracingConfig) -> Result<(), TracingError> {
    let tracer = opentelemetry_jaeger::new_agent_pipeline()
        .with_service_name(&config.service_name)
        .install_simple()?;

    tracing_subscriber::registry()
        .with(tracing_opentelemetry::layer().with_tracer(tracer))
        .with(tracing_subscriber::fmt::layer())
        .init();

    Ok(())
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

For complex applications, organize as services:

```
my-service-platform/
├── shared/                      # Cross-cutting infrastructure
│   ├── shared-types/           # Domain primitives
│   ├── shared-config/          # Configuration management
│   └── shared-observability/   # Metrics & tracing
├── services/                   # Business services
│   ├── core-services/          # Core business logic
│   │   ├── service-core/       # Domain logic
│   │   ├── service-api/        # HTTP endpoints
│   │   └── service-worker/     # Background processing
│   └── business-services/      # Business-specific services
│       ├── business-core/      # Business logic
│       ├── business-api/       # API endpoints
│       └── business-processor/ # Workflow engine
└── platform/                  # Platform services
    ├── gateway/                # API gateway
    ├── admin-panel/            # Administration
    └── health-monitor/         # Health monitoring
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

// Using configuration patterns triggers config dependencies
#[derive(Deserialize, Validate)]
struct Config { /* ... */ }
    // Cursor suggests: Add figment, validator, arc-swap

// Using metrics patterns triggers observability dependencies
let counter = metrics.counter("requests_total");
    // Cursor suggests: Add prometheus, dashmap, opentelemetry
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
2. **Web APIs** - Core + Axum + Database + Observability rules
3. **Service Applications** - Core + Configuration + Observability + Utilities
4. **Complex Systems** - All rules including workspace organization

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

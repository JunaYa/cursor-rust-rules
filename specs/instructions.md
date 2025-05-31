# Instructions

请构建 Rust CLI 项目的 rules:

1. 如果项目需要使用到 CLI，则引入 clap，使用 derive feature。
2. 如果有多个 CLI，请使用 subcommand。
3. 构建应用于 command 的 execute trait，每个 subcommand 实现该 trait，并使用 enum_dispatch 进行 dispatch

```rust
#[enum_dispatch(...)]
pub trait CommandExecutor {
    async fn execute(&self, args: &Args) -> Result<(), Error>;
}
```

4. 其他请遵循 clap 最佳实践

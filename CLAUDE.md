# LLM Gateway for .NET

Self-hosted LLM gateway (ASP.NET Core + YARP + Aspire). Full design, pipeline,
data model and milestones live in `docs/PLAN.md` (local only, not in git);
read it before starting work if it exists.

This is a learning project: the owner is learning ASP.NET + AI. Explain the
reasoning behind non-obvious code, and work one milestone (M0, M1, …) at a time.

## Commands
- Run everything (needs Docker): `dotnet run --project src/LlmGateway.AppHost`
- Build: `dotnet build`
- Test: `dotnet test`
- Format (run before every commit): `dotnet format`
- Same checks as CI: `dotnet format --verify-no-changes && GITHUB_ACTIONS=true dotnet build -c Release && dotnet test -c Release --no-build`

## Layout
- `src/LlmGateway.Core`: pure logic, no ASP.NET references. Keep it that way so it stays unit-testable.
- `src/LlmGateway.Data`: EF Core (Npgsql). Migrations go here.
- `src/LlmGateway.Gateway`: proxy host. Uses YARP `IHttpForwarder` (direct forwarding), not config-based `MapReverseProxy`.
- `src/LlmGateway.MockProvider`: fake Anthropic/OpenAI API. Use it for dev and tests instead of real providers.
- `src/LlmGateway.AppHost` / `ServiceDefaults`: Aspire.

## Conventions
- .NET 10, nullable enabled, file-scoped namespaces.
- Shared build settings live in `Directory.Build.props`; don't repeat `TargetFramework`, `Nullable` or `ImplicitUsings` in project files.
- Code analysis: `AnalysisLevel=latest-recommended` + Meziantou.Analyzer, style rules enforced in build via `.editorconfig`.
  Warnings are errors in CI. Fix warnings rather than suppressing them; if a suppression is truly needed,
  scope it narrowly in `.editorconfig` with a comment explaining why.
- Line endings are LF (`.gitattributes`).
- Tests: xUnit v3 + Shouldly (not FluentAssertions, which needs a commercial license from v8).
- Never log request/response bodies or API keys. Store API keys as SHA-256 hashes only.
- Provider API keys come from user-secrets / Aspire parameters, never from committed config.
- Gateway errors use the provider's native error JSON shape.
- Commit messages: Conventional Commits (`feat:`, `fix:`, `chore:`, `test:`, `docs:`).

## Known issues
- Build warning NU1902 (MessagePack 2.5.x) comes from Aspire's own dependencies. Don't force-upgrade MessagePack; wait for an Aspire update.

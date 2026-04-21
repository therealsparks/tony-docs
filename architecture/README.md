# Architecture

[← back to index](../README.md)

Tony has two halves: a **backend** (his brain — the server running OpenClaw, scripts, and integrations) and a **frontend** (his face — the dashboard site at `tony.austinvisuals.com` that the team and Matt visit). The pages below describe how those halves work and how they stay in sync.

1. **[Components](01-components.md)** — Map of every component and how they connect.
2. **[Publish loop](02-publish-loop.md)** — How the frontend stays current. Covers the 15-minute heartbeat and on-demand content pushes.
3. **[Command loop](03-command-loop.md)** — How an email becomes an action (Dropbox filing, project update, video generation, etc.).

Diagrams are rendered with [Mermaid](https://mermaid.js.org/), which GitHub displays inline.

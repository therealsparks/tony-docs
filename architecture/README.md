# Architecture

[← back to index](../README.md)

The Tony system consists of a conversational AI runtime (**OpenClaw**), a workspace directory of scripts and configuration, and a GitHub Pages publish target. The three pages below describe how those components interact.

1. **[Components](01-components.md)** — Map of the system's components and connections.
2. **[Publish loop](02-publish-loop.md)** — How the dashboards at [therealsparks.github.io/tony](https://therealsparks.github.io/tony/) stay current. Covers the 15-minute heartbeat and content-update pushes.
3. **[Command loop](03-command-loop.md)** — How an email becomes a handler action (Dropbox filing, project registry update, video generation, etc.).

Diagrams are rendered with [Mermaid](https://mermaid.js.org/), which GitHub displays inline.

# Architecture

[← back to index](../README.md)

Tony is not a single app — he's a collection of scripts glued together by a conversational AI runtime (**OpenClaw**), a shared workspace directory, and a GitHub Pages publish target. That's why he feels like a black box from the outside.

These three pages break the system into independent loops. They're designed to be read in order, but each stands on its own if you only need one.

1. **[Components](01-components.md)** — What talks to what. The "map" of Tony's world. Start here if you've never looked under the hood.
2. **[Publish loop](02-publish-loop.md)** — How the live dashboards at [therealsparks.github.io/tony](https://therealsparks.github.io/tony/) stay fresh. Explains the ~15-minute heartbeat and the content update pushes.
3. **[Command loop](03-command-loop.md)** — How an AV team member's email becomes a Tony action (filing to Dropbox, updating a project, generating a video, etc.).

All diagrams use [Mermaid](https://mermaid.js.org/), which GitHub renders natively — you should see them inline when viewing these files on github.com.

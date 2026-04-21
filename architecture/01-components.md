# 1. Components — what talks to what

[← architecture index](README.md) · [← docs home](../README.md)

This is the big-picture map of the Tony system as it runs today. Matt's laptop is the brain; everything else is either an external service Tony talks to or an output Tony produces.

```mermaid
flowchart LR
    subgraph laptop["🖥️ Matt's laptop — ARM64 Windows"]
        direction TB
        CL["ClawLauncher.exe<br/>(Go GUI on :54321)"]
        RT["myclaw/<br/>OpenClaw runtime<br/>Node 24 + llama.cpp"]
        WS["📁 .openclaw/workspace/<br/>= TonyWorkspace on disk<br/>AGENTS · SOUL · USER ·<br/>175 scripts · 3 skills · data"]
        SEC[🔑 .openclaw/secrets/<br/>Dropbox · Gmail · QBO<br/>GitHub PAT · GA4 · AI keys]
        CRON["⏱️ Windows Task Scheduler<br/>heartbeat every 15 min<br/>QBO sync · watchdogs"]
        MEM[(🧠 memory/<br/>runtime state)]

        CL --> RT
        RT --> WS
        WS <-.reads.-> SEC
        WS <--> MEM
        CRON -->|triggers scripts| WS
    end

    subgraph ext["☁️ External services Tony integrates with"]
        direction TB
        GM["📧 Gmail<br/>tony@ · bot@ · matt@"]
        DB["📦 Dropbox<br/>info@austinvisuals.com"]
        GOOG["📄 Google Drive / Docs / Sheets"]
        QBO["💰 QuickBooks Online"]
        GA[📊 GA4 Analytics]
        AI["🤖 AI APIs<br/>Anthropic · Vertex Veo<br/>Replicate · ElevenLabs"]
        WP["🌐 WordPress<br/>austinvisuals.com"]
    end

    WS <-->|OAuth| GM
    WS <-->|API| DB
    WS <-->|service acct| GOOG
    WS <-->|OAuth| QBO
    WS -->|pull| GA
    WS -->|generate| AI
    WS -->|REST| WP

    WS -->|"git push<br/>(deploy_*.py)"| REPO

    subgraph github["🐙 GitHub"]
        REPO["therealsparks/tony repo<br/>(7,473 commits)"]
        PAGES["GitHub Pages →<br/>therealsparks.github.io/tony/"]
        REPO --> PAGES
    end

    subgraph humans["👥 Consumers"]
        MATT["Matt<br/>views dashboards"]
        TEAM["AV team<br/>nancy · david · tom<br/>adam · peter · che"]
    end

    PAGES -->|dashboards| MATT
    TEAM -->|email commands| GM
    GM -->|confirmations| TEAM

    classDef missing fill:#fee,stroke:#c33,stroke-dasharray:5 5
    class SEC,MEM missing
```

> **🔴 Red-dashed boxes = not delivered yet.** Need from Matt: `.openclaw/secrets/` and `memory/` before we can run anything ourselves. See [migration/missing-pieces.md](../migration/missing-pieces.md).

## What each piece is, in plain terms

- **ClawLauncher.exe** — A small GUI app Matt clicks to start Tony. It opens a local web page at `localhost:54321` and runs the `openclaw` command under the hood.
- **myclaw/** — The actual Tony runtime. It's a Node.js install with ~373 packages, including the `openclaw` package, `node-llama-cpp`, and SDKs for every service Tony talks to.
- **Workspace** — The folder Tony reads from every session: identity files (who he is), 175 automation scripts (what he does), three skills (specialized capabilities), and data (projects, processes, reference).
- **Secrets** — Credentials Tony needs. Stored separately from the workspace so sharing the workspace doesn't leak keys. **Not included in what Matt shared with us.**
- **Memory** — Tony's notes to himself between sessions. Regenerates on its own.
- **Task Scheduler** — Windows cron. Fires Tony's periodic jobs: the 15-minute heartbeat, QuickBooks sync, watchdogs.
- **External services** — The seven buckets Tony talks to. He's got OAuth tokens or API keys for each.
- **GitHub repo + Pages** — The publish target. Tony generates HTML/JSON dashboards, pushes them to `therealsparks/tony` on GitHub, and GitHub Pages serves them as a website.

---

**Next:** [Publish loop →](02-publish-loop.md)

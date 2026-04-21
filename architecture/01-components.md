# 1. Components — what talks to what

[← architecture index](README.md) · [← docs home](../README.md)

Components are grouped into three domains: the host server (runtime, workspace, scheduled tasks), external services the system integrates with, and the GitHub publish target.

```mermaid
flowchart LR
    subgraph host["🖥️ Where Tony runs (server)"]
        direction TB
        RT["OpenClaw runtime<br/>Node 24 + llama.cpp"]
        WS["📁 workspace/<br/>AGENTS · SOUL · USER ·<br/>scripts · skills · data"]
        SEC[🔑 secrets/<br/>Dropbox · Gmail · QBO<br/>GitHub PAT · GA4 · AI keys]
        CRON["⏱️ scheduled tasks<br/>heartbeat every 15 min<br/>QBO sync · watchdogs"]
        MEM[(🧠 memory/<br/>runtime state)]

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

> **🔴 Red-dashed boxes = not visible in the delivered material.** `secrets/` and `memory/` live on the server and were not included in the bundles shared to date.

## Components

- **OpenClaw runtime** — The Node.js runtime. Installs the `openclaw` package, `node-llama-cpp`, and SDKs for each integrated service.
- **Workspace** — Source directory loaded each session: identity and policy files, automation scripts, skills, and reference data.
- **Secrets** — Credentials, kept separate from the workspace so the workspace can be shared without leaking keys. Not included in the delivered material.
- **Memory** — Between-session state. Regenerates automatically.
- **Scheduled tasks** — The host's job scheduler (cron, systemd timers, or Windows Task Scheduler — not determined from the delivered material). Runs the 15-minute heartbeat, QuickBooks sync, and watchdogs.
- **External services** — Seven external integrations, accessed via OAuth tokens or service-account keys.
- **GitHub repo + Pages** — The publish target. HTML and JSON dashboards are pushed to `therealsparks/tony` and served by GitHub Pages.

---

**Next:** [Publish loop →](02-publish-loop.md)

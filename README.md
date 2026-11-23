# SFMC Architecture Skill

An [Agent Skill](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) that gives AI coding assistants such as Claude reliable architecture and implementation guidance for **Salesforce Marketing Cloud Engagement (SFMC)**.

Use it to design, build, review, troubleshoot, and explain SFMC solutions. It covers AMPscript, Server-Side JavaScript (SSJS), SQL Query Activities, CloudPages, Journey Builder, Automation Studio, Data Extensions, the REST and SOAP APIs, WSProxy, and Email and Mobile Studio.

## Why this exists

SFMC is not a general-purpose development platform. It is a set of proprietary runtimes, each with its own capabilities and limits. AI models often assume SFMC behaves like Node.js, a modern browser, full Microsoft SQL Server, or .NET, and produce code that looks plausible but will not run, or that runs unsafely.

This skill corrects that by giving the assistant:

- **Verified platform facts:** what each SFMC runtime can and cannot do, and the source to defer to when there is doubt.
- **An architecture policy:** a fixed reasoning sequence, a technology decision hierarchy, and rules for choosing an execution context.
- **Security, performance, and error-handling standards:** applied to every design and code sample.
- **Reusable patterns, examples, and snippets:** production-oriented starting points, with validation, logging, and failure handling built in.

When information is missing, the assistant is told to stop and ask rather than guess. It must also mark unverified behavior as uncertain instead of inventing support.

## What's in the repository

The skill lives in the [`sfmc-architecture/`](sfmc-architecture/) folder:

```text
sfmc-architecture/
├── SKILL.md                          Entry point: architecture policy, decision rules, reading guide
├── agents/openai.yaml                Display metadata for OpenAI Codex
└── references/
    ├── SFMC_PLATFORM_FACTS.md        Highest authority: platform capabilities and limits
    ├── 00_Governance/                Document conventions, style guide, research and citation policy
    ├── 01_Foundations/               Platform landscape, execution contexts, data identity and tenancy,
    │                                 technology selection, security, performance, and operations
    ├── 02_Runtimes/                  AMPscript, SSJS, SQL Query Activities, CloudPages
    ├── 03_Channels_and_Orchestration/  Email, mobile messaging, Journey Builder, Automation Studio
    ├── 04_Integrations/              REST API, SOAP and WSProxy, integration reliability
    ├── 05_Data_Architecture/         Contact and subscriber model, Data Extensions, SQL design patterns
    ├── 06_Solution_Patterns/         Personalization, event-driven journeys, scheduled data pipelines,
    │                                 secure data capture, API integrations
    ├── 07_Reusable_Assets/           Complete examples and smaller snippets, by language
    ├── 08_Operations/                Observability and logging, release checklist, troubleshooting
    ├── deep_dives/                   In-depth AMPscript, SSJS, and SQL references
    └── maintenance/                  Prompts for auditing the library against Salesforce documentation
```

`SKILL.md` is always loaded when the skill is in use. The reference files are opened only when a task needs them, which keeps the assistant's context small.

When sources disagree, the skill follows this order of authority:

1. `SFMC_PLATFORM_FACTS.md`
2. `SKILL.md`
3. The governance documents
4. All other reference documents

## Installation

### Claude Code

Copy the skill folder into your personal skills directory, so it's available in every project:

```bash
git clone https://github.com/Ben-Shuttleworth/sfmc-architecture-skill.git
mkdir -p ~/.claude/skills
cp -R sfmc-architecture-skill/sfmc-architecture ~/.claude/skills/
```

To share it with a team through a single project instead, copy the folder into that project's `.claude/skills/` directory and commit it.

### Claude apps

Zip the `sfmc-architecture` folder and upload it as a custom skill in Claude's settings.

### Other agents

The skill follows the open Agent Skills format: a `SKILL.md` file with YAML front matter, plus supporting files. `agents/openai.yaml` supplies the display name, description, and default prompt for OpenAI Codex.

## Usage

You don't need to call the skill by name. The assistant loads it automatically when a request is about SFMC. For example:

- "Design a CloudPage that captures newsletter sign-ups and writes them to a Data Extension."
- "Review this SSJS script for unsupported syntax and error handling."
- "Should this nightly segmentation run as SQL in Automation Studio or in Journey Builder?"
- "Write a SQL Query Activity that deduplicates contacts by SubscriberKey."
- "Why does this AMPscript lookup fail at send time but work in preview?"

The skill is scoped to Salesforce Marketing Cloud Engagement. It isn't meant for other email service providers or non-Salesforce email tools.

## Keeping it current

Salesforce changes SFMC regularly. [`references/maintenance/`](sfmc-architecture/references/maintenance/) contains two prompts for keeping the library accurate:

1. **Technical audit:** checks every document against current official Salesforce documentation and reports what is inaccurate, outdated, or missing. It doesn't modify any files.
2. **Remediation:** applies the approved audit findings while keeping the library's structure, terminology, and style.

Run them periodically, and recheck limits, entitlements, and API behavior in particular, since these change most often.

## Disclaimer

This is an independent project. It is not affiliated with or endorsed by Salesforce. Salesforce and Marketing Cloud are trademarks of Salesforce, Inc.

The guidance aims to follow official Salesforce documentation, but platform behavior changes. Verify capabilities and limits against the [official documentation](https://developer.salesforce.com/docs/marketing/marketing-cloud/guide/programmatic-content-overview.html) before relying on them in production. Examples and snippets use placeholder names and IDs, which you must replace with your own configuration.

# AI SDLC frameworks

A reference collection of open-source frameworks for AI-assisted software development, pinned as git submodules so they can be read, compared, and diffed side by side.

This repo holds no framework code of its own. Each folder is a pointer to an upstream repository at a specific commit, and each upstream project keeps its own license.

## Getting the code

```sh
git clone --recurse-submodules https://github.com/kevinondanet/frameworks.git
```

If you already cloned without `--recurse-submodules`, the folders will be empty until you run:

```sh
git submodule update --init
```

To move one framework to the latest commit on its tracked branch, then record the new pin:

```sh
git submodule update --remote <folder>
git commit -am "chore: bump <folder>"
```

## At a glance

Versions are as of the pinned commits on 2026-09-17 and will drift as submodules are bumped.

| Folder | Upstream | Category | Form factor | Version | License |
|---|---|---|---|---|---|
| `spec-kit-main` | [github/spec-kit](https://github.com/github/spec-kit) | Spec-driven workflow | Python CLI + markdown templates | 1.0.2.dev0 | MIT |
| `OpenSpec-main` | [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec) | Spec-driven workflow | TypeScript npm CLI | 1.10.0 | MIT |
| `BMAD-METHOD-main` | [bmad-code-org/BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) | Agile method with role agents | Node installer + markdown skill pack | 6.11.0 | MIT |
| `gsd-core-next` | [open-gsd/gsd-core](https://github.com/open-gsd/gsd-core) (`next` branch) | Context-engineered phase loop | TypeScript npm package + markdown agents | 1.11.0 | MIT |
| `superpowers-main` | [obra/Superpowers](https://github.com/obra/Superpowers) | Auto-triggered skills methodology | Markdown skill pack, per-harness plugins | 6.3.0 | MIT |
| `hve-core-main` | [microsoft/hve-core](https://github.com/microsoft/hve-core) | Agentic SDLC for Copilot | VS Code extension + Copilot CLI plugin | 3.2.2 | MIT (some content CC BY-SA 4.0) |
| `sample-aidlc-kiro-power-main` | [aws-samples/sample-aidlc-kiro-power](https://github.com/aws-samples/sample-aidlc-kiro-power) | Adaptive lifecycle methodology | Markdown-only Kiro Power | not stated | MIT-0 |
| `KiroCrew-main` | [kirodotdev/KiroCrew](https://github.com/kirodotdev/KiroCrew) | Persistent agent runtime | Python backend + React dashboard | 0.5.0 | Apache-2.0 |
| `RAMPART-main` | [microsoft/RAMPART](https://github.com/microsoft/RAMPART) | Agent safety and security testing | Python package / pytest plugin | dynamic (VCS) | MIT |

Seven of the nine define a development workflow. The last two are different kinds of tool: KiroCrew is a runtime that agents live in, and RAMPART tests the safety of agents you build.

### Workflows compared

| Framework | Workflow | Targets |
|---|---|---|
| Spec Kit | constitution → specify → plan → tasks → implement → converge | 30+ agents |
| OpenSpec | proposal → specs → design → tasks → implement, then archive | 30+ tools |
| BMAD Method | Clarify → Plan → Build and verify → Learn and adjust | 47 platforms in the installer |
| GSD Core | Discuss → Plan → Execute → Verify → Ship, per milestone | Claude Code, Codex, Copilot, Cursor, and others |
| Superpowers | brainstorm → worktree → plan → subagent execution → TDD → review → finish | 14 harnesses |
| HVE Core | Research → Plan → Implement → Review (RPI) | GitHub Copilot only |
| AI-DLC | Inception → Construction → Operations, with stages skipped adaptively | Kiro only |

## Workflow frameworks

### Spec Kit — `spec-kit-main`

GitHub's open-source toolkit for spec-driven development with any AI coding agent. Its premise is that specifications become executable and directly generate implementations.

- **Workflow:** slash commands `/speckit.constitution`, `/speckit.specify`, `/speckit.plan`, `/speckit.tasks`, `/speckit.implement`, and `/speckit.converge`, repeating implement and converge until converged. Optional commands: `/speckit.clarify`, `/speckit.analyze`, `/speckit.checklist`, `/speckit.taskstoissues`. Opt-in extensions add a bug workflow (assess → fix → test) and an idea-assessment workflow.
- **Works with:** 30+ CLI and IDE agents; the README names GitHub Copilot, Claude, Codex CLI, and Command Code. `specify integration list` prints the full set.
- **Install:** replace `vX.Y.Z` with a release tag.

  ```sh
  uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@vX.Y.Z
  specify init my-project --integration copilot
  ```

- **Implementation:** Python CLI (`specify`), Python 3.11+ and uv, that writes markdown command templates into each agent's directory.
- **What sets it apart:** layered customization. Project-local overrides, stackable presets, extensions, and role-based bundles (`bundle.yml`) let an organization reshape the templates and terminology rather than accept one fixed spec format.

### OpenSpec — `OpenSpec-main`

An "AI-native system for spec-driven development": plain-Markdown specs that the AI writes and you review before any code exists. Its stated philosophy is "fluid not rigid, iterative not waterfall, easy not complex, built for brownfield not just greenfield."

- **Workflow:** the OPSX artifact-guided flow, framed as actions with dependencies rather than fixed phases. The default `core` profile provides `/opsx:explore`, `/opsx:propose`, `/opsx:apply`, `/opsx:update`, `/opsx:sync`, and `/opsx:archive`; an expanded profile adds `/opsx:new`, `/continue`, `/ff`, `/verify`, `/bulk-archive`, and `/onboard`. Each change lives in `openspec/changes/<id>/` as `proposal.md`, `specs/`, `design.md`, and `tasks.md`, then archives into the living spec set. Requirements use `SHALL` statements with `#### Scenario:` WHEN/THEN blocks.
- **Works with:** 30+ tools, including Claude Code, Cursor, GitHub Copilot, Codex, Gemini CLI, Amazon Q Developer, Kiro, Devin, and Zed Agent.
- **Install:**

  ```sh
  npm install -g @fission-ai/openspec@latest
  openspec init
  ```

- **Implementation:** TypeScript npm CLI, Node 20.19.0+, that generates skills and command files.
- **What sets it apart:** the prompts are user-editable data (`schema.yaml` and `templates/*.md`) rather than hardcoded in the package, and it targets brownfield and cross-repo work. A beta "Stores" feature keeps planning in its own repository.

### BMAD Method — `BMAD-METHOD-main`

"Agile Ai Driven Development — turn an idea or change request into working software without giving up the thinking." It treats AI-driven development as covering the whole effort — what to build, how it holds together, how it changes — rather than code generation alone.

- **Workflow:** a delivery loop of Clarify → Plan → Build and verify → Learn and adjust, sized to the work so that small changes go straight to build. Skill packs live under `src/bmm-skills/`:
  - Role agents: `bmad-agent-analyst`, `bmad-agent-architect`, `bmad-agent-dev`, `bmad-agent-pm`, `bmad-agent-ux-designer`
  - Planning: `bmad-prd`, `bmad-architecture`, `bmad-spec`, `bmad-create-epics-and-stories`, `bmad-sprint-planning`
  - Shipping: `bmad-build`, `bmad-code-review`, `bmad-correct-course`, `bmad-retrospective`
  - Core: `bmad-help`, `bmad-brainstorming`, `bmad-party-mode`
- **Works with:** 47 platforms in the installer's platform list, including Claude Code, Cursor, Codex, Gemini CLI, GitHub Copilot, Kiro, and Windsurf. Web bundles target Gemini Gems and ChatGPT Custom GPTs.
- **Install:**

  ```sh
  npx bmad-method install
  ```

- **Implementation:** Node.js CLI installer that lays down a markdown skill, agent, and workflow pack. Requires Node 20.12+, Python 3.10+, and uv.
- **What sets it apart:** it is an agile method with named role agents and a modular ecosystem (BMad Builder, Test Architect, Loop, Game Dev Studio), not a single fixed spec-to-code pipeline.

### GSD Core — `gsd-core-next`

"Git. Ship. Done." A lightweight meta-prompting, context-engineering, and spec-driven system. The problem it targets is "context rot": output quality degrading as the AI fills its context window. This submodule tracks the upstream `next` branch.

- **Workflow:** each milestone repeats Discuss → Plan → Execute → Verify → Ship. Research, planning, and execution run in fresh-context subagents, and `STATE.md` and `CONTEXT.md` carry state across sessions. It ships about 34 named agents (`gsd-planner`, `gsd-executor`, `gsd-verifier`, `gsd-code-reviewer`, `gsd-roadmapper`, `gsd-security-auditor`, and others) and a large command set under `commands/gsd/`, such as `/gsd-new-project`, `/gsd-onboard`, `discuss-phase`, `execute-phase`, and `complete-milestone`.
- **Works with:** Claude Code, OpenCode, Antigravity CLI, Kimi CLI, Kilo, Codex, Copilot, Cursor, Windsurf, and more.
- **Install:**

  ```sh
  npx @opengsd/gsd-core@latest
  ```

- **Implementation:** TypeScript npm package with an installer CLI, bundled markdown agents and commands, a Claude plugin manifest, and an MCP server binary.
- **What sets it apart:** context engineering rather than spec format. Plans are sized to fit a fresh context window, every executor starts clean, and Verify is mandatory before a phase ships.

### Superpowers — `superpowers-main`

"A complete software development methodology for your coding agents, built on top of a set of composable skills." The skills trigger automatically, so there is nothing to invoke.

- **Workflow:** seven skills in sequence — `brainstorming` (Socratic spec refinement) → `using-git-worktrees` → `writing-plans` (2–5 minute tasks with exact file paths) → `subagent-driven-development` or `executing-plans` (a fresh subagent per task with two-stage review) → `test-driven-development` (red-green-refactor) → `requesting-code-review` → `finishing-a-development-branch`. The library also includes `systematic-debugging`, `verification-before-completion`, `dispatching-parallel-agents`, `receiving-code-review`, and `writing-skills`.
- **Works with:** Claude Code, Antigravity, Codex App, Codex CLI, Cursor, Devin CLI, Factory Droid, Gemini CLI, GitHub Copilot CLI, Grok Build CLI, Kimi Code, OpenCode, Pi, and Hermes Agent, each installed separately.
- **Install (Claude Code):**

  ```
  /plugin install superpowers@claude-plugins-official
  ```

- **Implementation:** zero-dependency markdown skill pack with per-harness adapters (`.claude-plugin/`, `.codex-plugin/`, `.cursor-plugin/`, `.opencode/`, `gemini-extension.json`) and hooks.
- **What sets it apart:** enforcement rather than invocation. Skills are described as "mandatory workflows, not suggestions," with strict TDD and subagent-driven delivery. Note that it has an optional telemetry ping, disabled with `SUPERPOWERS_DISABLE_TELEMETRY`.

### HVE Core — `hve-core-main`

Microsoft's Hypervelocity Engineering core: a prompt library and agentic SDLC framework combining agents, prompts, coding instructions, and validated skills for GitHub Copilot. The README cautions that it is highly opinionated and fast-moving, and is best treated as a source of patterns rather than a production dependency.

- **Workflow:** the RPI lifecycle — Research, Plan, Implement, Review, plus Follow-up — entered through the `RPI Agent` chat mode, `/rpi`, `/rpi-quick`, or individual phase skills such as `/rpi-research` and `/rpi-plan`. Research is read-only and runs only when existing evidence is inadequate. Artifacts land under `.copilot-tracking/research/`. Content spans security, accessibility, responsible AI, data science, design thinking, and project planning, and `/hve-builder` helps fork the patterns.
- **Works with:** GitHub Copilot in VS Code and the GitHub Copilot CLI.
- **Install:** the HVE Core extension from the VS Code Marketplace, or:

  ```sh
  copilot plugin marketplace add microsoft/hve-core
  copilot plugin install hve-core@hve-core
  ```

- **Implementation:** markdown prompt, agent, and skill pack delivered as a VS Code extension and Copilot CLI plugin. Tooling and tests are PowerShell (Pester).
- **What sets it apart:** evidence-first discipline. Every artifact passes a CI validation pipeline with quality gates, backed by formal governance and a Responsible AI transparency note.

### AI-DLC Kiro Power — `sample-aidlc-kiro-power-main`

An AWS sample that packages AI-DLC (AI-Driven Development Life Cycle) as a Kiro "Power." It analyzes the request and the existing codebase to decide which lifecycle stages add value and which to skip.

- **Workflow:** three phases.
  - **Inception:** Workspace Detection, Requirements Analysis, and Workflow Planning always run. Reverse Engineering, User Stories, Application Design, and Units Generation are conditional.
  - **Construction:** Code Generation and Build and Test always run. Functional Design, NFR Requirements, NFR Design, and Infrastructure Design are conditional.
  - **Operations:** marked as future expansion.

  Quality gates require explicit approval, clarifying questions are multiple-choice and answered with an `[Answer]:` tag, and artifacts are written to `aidlc-docs/` (`aidlc-state.md`, `audit.md`, `inception/`, `construction/`).
- **Works with:** Kiro IDE with Powers enabled. No MCP servers required.
- **Install:** search for "ai-dlc-methodology" in Kiro's Powers panel, or copy the `ai-dlc-methodology` folder into the Kiro powers directory.
- **Implementation:** markdown only — `powers/ai-dlc-methodology/POWER.md` plus `steering/` and `workflows/` files loaded on demand. No code and no dependencies.
- **What sets it apart:** stages are skipped rather than run as a fixed pipeline, and a decision audit trail is kept for governance.

## Agent runtime

### Kiro Crew — `KiroCrew-main`

"A persistent workspace for development work that self-improves and continues beyond one session." This is not a spec-authoring workflow. It is a long-running agent runtime that you host locally or on your own remote hardware.

- **Architecture:** Surfaces → Gateway → agent sessions, where sessions use an ACP runtime to drive `kiro-cli` and MCP tools. Concepts include persistent sessions, durable "lessons" learned from corrections, synthesized reusable skills, checkpointed long-running tasks, cron and heartbeat unattended work, spawned subagents, approvals and governance profiles, and "Kiro Crew Apps." CLI verbs: `kirocrew chat`, `run`, `cron`, `spawn`, `security`, `service install`.
- **Works with:** `kiro-cli` over ACP only. Surfaces are a desktop app, a web dashboard on `localhost:5476`, the CLI, and Slack, Discord, Telegram, Teams, Webex, WeCom, and WeChat.
- **Install:**

  ```sh
  curl -fsSL https://download.crew.kiro.dev/cli.sh | sh
  ```

- **Implementation:** Python package `kiro_crew` (3.10+) with a React, TypeScript, and Vite dashboard. Also ships as an Electron desktop app, a Docker image, and Linux packages.
- **What sets it apart:** persistence and unattended autonomy are the product — memory, scheduling, and sandboxing rather than spec artifacts.

## Agent safety testing

### RAMPART — `RAMPART-main`

The Microsoft AI Red Team's "Risk Assessment & Measurement Platform for Agentic Red Teaming": a pytest-native safety and security testing framework for agentic AI applications. It is not a development workflow. It belongs here as the verification side of an AI SDLC — testing the agents you build.

- **How it works:** you write safety tests as ordinary pytest tests against your own agent. You implement an `AgentAdapter` and `Session` protocol to bridge your agent and describe it with an `AppManifest`. The package provides `attacks`, `probes`, `evaluators`, `converters`, `payloads`, `surfaces`, and `reporting` modules plus a `pytest_plugin`. Coverage spans adversarial attacks such as cross-prompt injection (XPIA), benign failures, and harm categories.
- **Works with:** any agent you can wrap in an adapter; the documented examples are an OpenAI client, HTTP, gRPC, and a Playwright browser session. Built on Microsoft PyRIT v0.13.0.
- **Install:**

  ```sh
  uv add rampart
  ```

- **Implementation:** Python 3.11+ package published to PyPI as a library and pytest plugin. The version is derived from VCS tags.
- **What sets it apart:** agent safety testing becomes a normal part of the test suite, with assertions running under pytest and CI rather than as a separate red-team exercise.

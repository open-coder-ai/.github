<div align="center">

<p><img src="https://raw.githubusercontent.com/open-coder-ai/chock/main/docs/assets/readme/cover-org.png" alt="The open-coder-ai mark, a gold chevron and diamond, on a dusk-blue background." width="100%"></p>

</div>

# Teach your AI agent what not to do.

Open-source guardrails for AI coding agents: rules the agent reads, checks that run as it writes, and gates at commit and in CI.

[chock](https://github.com/open-coder-ai/chock) · [policy catalog](https://github.com/open-coder-ai/chock-catalog) · [agentseam](https://github.com/open-coder-ai/agentseam) · [context-report](https://github.com/open-coder-ai/context-report) · [threat intel](https://github.com/open-coder-ai/chock-threat-intel) · chock.sh (launching soon)

Open Coder AI builds open-source guardrails for AI coding agents. Chock is the engine: each policy is a deterministic local check, with no model and no upload, that refuses a known class of mistake before it is committed. The [catalog](https://github.com/open-coder-ai/chock-catalog) holds the policies, [agentseam](https://github.com/open-coder-ai/agentseam) is the layer that carries them into each agent, and everything is free and open source under Apache-2.0.

## Application security for the code your agents write

Coding agents already ask before they run a shell command. What they do not check is the code they write. Chock refuses the known classes of that: SQL injection and unsafe deserialization in Java, a wildcard IAM grant, an MCP server pulled at `@latest`, an unpinned GitHub Action, Trojan Source bidi and tag characters, a dependency that is not on your allowlist, a secret written into a file. Shell, git and key protection is included too, but it is not the point.

| What gets refused | Policy |
|---|---|
| Wildcard `Action`/`Resource`, `AdministratorAccess` in IAM | [`block-wildcard-iam`](https://github.com/open-coder-ai/chock-catalog/tree/main/agentic-security/block-wildcard-iam) |
| Agent components at `@latest` or `:latest` | [`block-unpinned-agent-components`](https://github.com/open-coder-ai/chock-catalog/tree/main/agentic-security/block-unpinned-agent-components) |
| `eval`/`exec`, shell-mode subprocess, unsafe deserialization | [`block-unsafe-code-execution`](https://github.com/open-coder-ai/chock-catalog/tree/main/agentic-security/block-unsafe-code-execution) |
| GitHub Actions not pinned to a commit | [`pin-github-actions`](https://github.com/open-coder-ai/chock-catalog/tree/main/base/pin-github-actions) |
| Bidi and tag Unicode characters (Trojan Source) | [`block-invisible-unicode`](https://github.com/open-coder-ai/chock-catalog/tree/main/base/block-invisible-unicode) |
| Dependencies off your allowlist | [`verify-dependency-exists`](https://github.com/open-coder-ai/chock-catalog/tree/main/base/verify-dependency-exists) |
| Secrets in a change, and secret files by path | [`scan-secrets`](https://github.com/open-coder-ai/chock-catalog/tree/main/base/scan-secrets) |
| Java and Kotlin vulnerability classes | [`java-security`](https://github.com/open-coder-ai/chock-catalog/tree/main/base/java-security) |

Chock does not replace code review, your SAST suite or a penetration test. It refuses known classes while the agent writes, so they are fixed before review.

## Install

chock is on PyPI, but the release there (0.15.2, 30 Sep 2026) is older than the engine this page describes. Install the frozen engine from its commit (Python 3.11 or newer):

```bash
pip install "chock @ git+https://github.com/open-coder-ai/chock@992711af4cf8d4fd9c4c861f10ef6e53374d75d7"
```

### Three ways to adopt it

**1. In your repository (for teams).** The commit gates are enforced at commit and in CI.

Run `chock init .`, then `chock add <id> --ref <catalog commit> --verify-sha <sha256> --skip-compile` once per policy, then `chock sync --repo . --ci`.

Commit the result. Every clone runs `chock sync --repo .` once, because git never clones hooks. Templates: [chock-quickstart](https://github.com/open-coder-ai/chock-quickstart) and [chock-example](https://github.com/open-coder-ai/chock-example).

**2. In your coding agent, as plugins.** Five plugin repositories, one per client. They are best-effort and fail open: the client's own hook runs the check, and nothing runs in CI. Per-client install lines are in each repository's README: [Claude Code](https://github.com/open-coder-ai/chock-claude-plugins), [Copilot](https://github.com/open-coder-ai/chock-copilot-plugins), [Cursor](https://github.com/open-coder-ai/chock-cursor-plugins), [Codex](https://github.com/open-coder-ai/chock-codex-plugins), [Devin](https://github.com/open-coder-ai/chock-devin-plugins).

**3. One plugin for your agent, from chock.sh (launching soon).** Pick catalog policies, or describe your own in a short interview, for Claude Code, Cursor, Codex, Copilot or Devin. You get a `chock install --selection … --client <agent>` command, or a setup script you download, read and run; chock builds the plugin on your machine and shows any custom code before it installs. Inside the agent you can switch a single policy off with `chock bundle off <id>`; the agent itself cannot switch one off.

## How it works

- **No model, no tokens.** Each check is a deterministic script. A check costs no tokens; a refusal adds one short reason to the agent's context.
- **Shift left.** The known classes are fixed in the agent's turn, by the same agent, before a human or CI sees them.
- **Code stays in the agent's environment.** Chock adds no new place your code goes: no upload to a scanning service and no API key. Your agent still sends context to its own model provider; Chock adds no additional destination. Installing fetches policies once; the checks run locally.
- **Three places a check can run:** in the agent (best-effort, fails open), at commit (git hooks) and in CI (a gate whose findings can be exported as SARIF). The fail-closed history scan and the policy baseline check for adopter CI come with the engine.

## What it stops

Numbers are from `registry.yaml` at chock-catalog `31d7d46`, measured 2026-10-06.

| Measure | Value |
|---|---|
| Policies | 71: 35 enforced at commit, 11 in the agent (best-effort), 25 advisory |
| Claude Code labels | 39 block, 3 ask, 5 warn, 24 advisory |
| Eval cases | 4,280, of which 4,098 run automatically |
| OWASP Top 10 for Agentic Applications | all 10 risks have a mapped policy, every mapping labelled partial; 7 have a slice refused at commit (ASI01–05, 07, 09), ASI10 is refused in the agent (best-effort) and asks a person at commit, ASI06 warns only, ASI08 is advisory only, none is fully covered |

No agent reaches "enforced" today. Advisory means rule text the agent reads; only a hook or gate that exits non-zero is a control. The OWASP row is derived from each manifest's `compliance.owasp_asi` joined to its registry tier, counting a slice only where its note says it refuses rather than warns; the full table is in [docs/coverage.md](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/coverage.md). Cross-agent numbers live in agentseam's matrix: at its `main` (`104da73`), `matrix.enforcement_level()` over 16 agents at `pre_tool` gives 11 best-effort, 1 enforceable (Cursor), 4 none and 0 enforced.

## Guardrails, not guarantees

Tiers: `commit` is a git hook or CI gate that exits non-zero. `in-agent` is the agent's pre-tool hook: best-effort, and it fails open. `advisory` is rule text the agent reads. No agent reaches `enforced` today. OWASP mappings are partial and the engine is frozen at the commit above. Chock does not stop every attack: it closes common, known entry points before they ship.

## FAQ for people and agents

**Does it use an LLM?** No. Every check is a deterministic script; a check costs no tokens.

**Does my code leave my machine?** Chock adds no new place your code goes. Your agent still sends context to its own model provider.

**Which agents does it work with?** Claude Code, Copilot, Cursor, Codex and Devin have plugin repositories. So far only Claude Code has been seen refusing in a live session; the others are built and tested but not yet witnessed in their own client, and are labelled untested until they are. agentseam maps 16 agents and says per agent what each can enforce.

**Repository or plugins?** Use the repository route for teams: it enforces at commit and in CI for every clone and every agent. Plugins add the agent's own hook, best-effort, with no repository changes.

**What does it cost?** Free and open source (Apache-2.0).

**Does it replace SAST or code review?** No. It refuses known classes while the agent writes, so they are fixed before review.

**Which OWASP items does it cover?** All ten Agentic Applications risks have a mapped policy; every mapping is partial. See the table above and [docs/coverage.md](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/coverage.md).

## For tools and agents

- [`registry.yaml`](https://github.com/open-coder-ai/chock-catalog/blob/main/registry.yaml): every policy, its tier and eval counts.
- [Policy manifests](https://github.com/open-coder-ai/chock-catalog/tree/main/base) (and [`agentic-security/`](https://github.com/open-coder-ai/chock-catalog/tree/main/agentic-security)): `compliance` mappings per policy.
- [`docs/coverage.md`](https://github.com/open-coder-ai/chock-catalog/blob/main/docs/coverage.md): OWASP coverage in full.
- Plugin catalogs: `.claude-plugin/marketplace.json` in [chock-claude-plugins](https://github.com/open-coder-ai/chock-claude-plugins/blob/main/.claude-plugin/marketplace.json), and the matching file in each other plugin repository.
- chock.sh `/llms.txt` and `/api/index.json`: coming with the site.

## Part of open-coder-ai

| Repository | What it is |
|---|---|
| [chock](https://github.com/open-coder-ai/chock) | The engine: author a policy once and compile it into git hooks, a CI gate or the agent's own pre-tool hook. |
| [chock-catalog](https://github.com/open-coder-ai/chock-catalog) | The policies, each labelled with what it actually enforces and with its eval counts. |
| [agentseam](https://github.com/open-coder-ai/agentseam) | One handler API over every coding agent's hooks, instruction files, plugins and config, with a matrix of what each agent can enforce. |
| [context-report](https://github.com/open-coder-ai/context-report) | An open, signed report format for whether an agent context artifact (plugin, hook, skill, `AGENTS.md`, MCP server) actually works. |
| [chock-threat-intel](https://github.com/open-coder-ai/chock-threat-intel) | A weekly, human-reviewed ledger of agentic-AI threats, each scored against the catalog. Unofficial compilation: verify at the source. |
| [chock-quickstart](https://github.com/open-coder-ai/chock-quickstart) | Template repository: exactly what `chock init` leaves behind. |
| [chock-example](https://github.com/open-coder-ai/chock-example) | Template repository: a working adoption with one policy per layer (hook, rule, skill). |
| [chock-claude-plugins](https://github.com/open-coder-ai/chock-claude-plugins) | Catalog policies as Claude Code plugins. |
| [chock-copilot-plugins](https://github.com/open-coder-ai/chock-copilot-plugins) | Catalog policies as GitHub Copilot and VS Code plugins. |
| [chock-cursor-plugins](https://github.com/open-coder-ai/chock-cursor-plugins) | Catalog policies as Cursor plugins. |
| [chock-codex-plugins](https://github.com/open-coder-ai/chock-codex-plugins) | Catalog policies as Codex plugins. |
| [chock-devin-plugins](https://github.com/open-coder-ai/chock-devin-plugins) | Catalog policies as Devin plugins; Devin documents plugin hooks as best-effort and fail-open. |
| chock.sh | Site and plugin builder, launching soon. |

## Contributing

Most contributions need no code: an eval case for a bypass, a "this policy is wrong" issue, a live-run check of an agent row in agentseam, an adapter for a new agent, a policy for a `policy wanted` entry in the threat ledger, or a `good first issue`. The core repositories (chock, chock-catalog, agentseam, context-report, chock-threat-intel) take DCO-signed commits (`git commit -s`); each repository's `SECURITY.md` says how to report a vulnerability privately.

Apache-2.0.

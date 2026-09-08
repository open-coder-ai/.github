<div align="center">

<img src="org-mark.svg" alt="open-coder-ai mark: two gold chevrons held apart around a solid diamond, on a navy rounded square." width="96">

# Open Coder AI

**Build for every AI coding agent at once — and know what actually holds.**

[agentseam](https://github.com/open-coder-ai/agentseam) ·
[chock](https://github.com/open-coder-ai/chock) ·
[policy catalog](https://github.com/open-coder-ai/chock-catalog) ·
[context-report](https://github.com/open-coder-ai/context-report) ·
[threat intel](https://github.com/open-coder-ai/chock-threat-intel)

</div>

Every coding agent invented its own way to be extended: its own hook events and payload
shapes, its own instruction file, its own plugin format, its own permission language, its own
way to say "no". So anything built for one agent — a guard, a linter, a memory rule, a
workflow skill, a telemetry tap, a cost meter — gets rewritten once per agent, or targets one
agent and stops there.

We build the layer underneath, and a few things on top of it.

**Underneath** is [agentseam](https://github.com/open-coder-ai/agentseam): one handler API
over every agent's hooks, instruction files, plugin packaging and config. Write one handler,
install it on every agent you use, and read a capability matrix that says what each agent can
actually do — enforce, best-effort, or only detect after the fact — with the evidence behind
every row. It is stdlib-only and it takes no position on what you build with it. Governance
was simply the first thing we built.

**On top**: [chock](https://github.com/open-coder-ai/chock), governance as code — write a
policy once and compile it into git hooks, CI gates and native pre-execution hooks; its
[catalog](https://github.com/open-coder-ai/chock-catalog) of ready policies, each labelled
with what it actually reaches; [context-report](https://github.com/open-coder-ai/context-report),
an open, signed report format for whether *any* context artifact — a plugin, a hook, a
skill, an `AGENTS.md`, an MCP server — actually works; and a weekly, human-reviewed
[threat digest](https://github.com/open-coder-ai/chock-threat-intel).

## The rule we hold ourselves to

> A rule an agent reads is advice. A hook that exits non-zero is a control. Both belong in
> a repo, and the difference has to be visible.

- **A claim must match a mechanism.** A capability an agent does not have is not in the
  matrix; a policy that does not exit non-zero is labelled advisory, whatever its manifest
  says.
- **Witnessed, not asserted.** A grade rises only after a real install was seen to do it.
  Vendor documentation earns a lower grade than a live run, and the grade records which it
  was.
- **Guardrails, not guarantees.** Pattern-based guards can be evaded, `--no-verify` skips a
  git hook, and a fresh clone enforces nothing until it is wired. Every repo's `SECURITY.md`
  says so up front.

## What we build

| Project | What it is | Start |
| :--- | :--- | :--- |
| [**agentseam**](https://github.com/open-coder-ai/agentseam) | The primitives layer: one handler API over every agent's hooks, instruction files, plugin packaging and config, with a capability matrix that carries its provenance. Build any cross-agent tool on it. | `pip install agentseam` |
| [**chock**](https://github.com/open-coder-ai/chock) | Governance as code, built on agentseam. One policy becomes a git hook, a CI gate, native hooks in Claude Code, Cursor, Copilot CLI and VS Code, and an `AGENTS.md` for the rest — plus a coverage report that says where a guarantee holds. | `pip install chock` |
| [**chock-catalog**](https://github.com/open-coder-ai/chock-catalog) | The policies: secret scanning, protected branches, destructive-command guards, an OWASP Agentic Security pack. Each is labelled with what it actually reaches. | `chock add scan-secrets` |
| [**context-report**](https://github.com/open-coder-ai/context-report) | An open, signed report format for one question: does this context artifact actually work? Reachability, fault behaviour, cost and efficacy, each row saying whether it is re-derivable or only claimed. | `pip install context-report` |
| [**chock-threat-intel**](https://github.com/open-coder-ai/chock-threat-intel) | A weekly, human-reviewed digest of agentic-AI threats, each scored against the catalog: enforced, advisory, or `policy wanted`. | [the ledger](https://github.com/open-coder-ai/chock-threat-intel/blob/main/reference/agentic-threat-ledger.md) |
| [**chock-claude-plugins**](https://github.com/open-coder-ai/chock-claude-plugins) · [copilot](https://github.com/open-coder-ai/chock-copilot-plugins) · [cursor](https://github.com/open-coder-ai/chock-cursor-plugins) · [codex](https://github.com/open-coder-ai/chock-codex-plugins) | The catalog compiled into each client's native plugin format. Generated only: CI rebuilds from source and fails on any difference. | add as a marketplace |
| [**chock-quickstart**](https://github.com/open-coder-ai/chock-quickstart) · [**chock-example**](https://github.com/open-coder-ai/chock-example) | Template repositories: exactly what `chock init` leaves behind, and a working adoption with one policy per layer. | *Use this template* |

## Two quick starts

One handler, every agent:

```python
from agentseam import run, Decision

def handler(event):
    if event.event == "pre_tool" and "AKIA" in (event.content or ""):
        return Decision.deny("no AWS keys in memory files")
    return Decision.allow()

run(handler)
```

```bash
agentseam install all "python3 my_handler.py" --events pre_tool
```

One policy, a blocked commit:

```bash
pip install chock
chock init
chock add protect-main-branch
chock sync
git commit -m "straight to main"    # exits non-zero, whichever agent or human typed it
```

## Contribute — most of it needs no code

- **Something new on the layer.** An adapter for an agent agentseam does not cover yet, or
  a tool of your own that runs on it — a linter, a memory rule, a cost meter. Start from the
  [adapter request](https://github.com/open-coder-ai/agentseam/issues/new?template=adapter_request.md)
  template, or just open an issue saying what you built.
- **Evidence from your own agent.** Most rows in the capability matrix rest on vendor
  documentation, not on anyone watching the agent run. If you have Cursor, Codex, Gemini CLI,
  Windsurf or another agent installed, [agentseam's probe](https://github.com/open-coder-ai/agentseam/blob/main/CONTRIBUTING.md#contributing-evidence-you-do-not-need-to-write-code)
  reports what your version actually does. A result that contradicts the matrix is the most
  valuable thing you can send.
- **"This policy is wrong."** An overstated policy is worse than a missing one. Open it on
  the [catalog](https://github.com/open-coder-ai/chock-catalog/issues/new/choose); that
  issue template exists for a reason.
- **A policy for your stack.** `chock init` installs a `policy-init` skill that scaffolds a
  conformant folder, and the `policy wanted` entries in the
  [threat ledger](https://github.com/open-coder-ai/chock-threat-intel/blob/main/reference/agentic-threat-ledger.md)
  are an open work list.
- **A first issue.** [`good first issue`](https://github.com/open-coder-ai/chock/labels/good%20first%20issue)
  on chock and on
  [context-report](https://github.com/open-coder-ai/context-report/labels/good%20first%20issue);
  comment to claim one.
- **Docs, diagrams, clearer CLI output.** First-class in every repo, and the fastest way in.

Every repository is Apache-2.0, takes DCO-signed commits (`git commit -s`), and reads the
whole diff before merge — agent-authored included. Use an agent if you want to; keep the
`Co-Authored-By` trailer it adds, and be able to defend every line.

## Where things happen

- **Questions and ideas** → Discussions on
  [chock](https://github.com/open-coder-ai/chock/discussions),
  [chock-catalog](https://github.com/open-coder-ai/chock-catalog/discussions) and
  [context-report](https://github.com/open-coder-ai/context-report/discussions); issues on
  agentseam. Telling us what is confusing is a contribution.
- **Bugs** → an issue on the repo that owns the source. The plugin repositories are
  compiled output; they point you to the catalog.
- **Vulnerabilities** → the private advisory linked from each repo's `SECURITY.md`, never a
  public issue.

<div align="center">

*A seam joins pieces cut separately into one thing that holds. That is the job.*

</div>

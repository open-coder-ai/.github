<div align="center">

<img src="org-mark.svg" alt="open-coder-ai organisation mark: two gold chevrons held apart around a solid diamond, on a navy rounded square." width="96">

# Open Coder AI

**Governance as code for AI coding agents — and an honest account of where it holds.**

[chock](https://github.com/open-coder-ai/chock) ·
[policy catalog](https://github.com/open-coder-ai/chock-catalog) ·
[agentseam](https://github.com/open-coder-ai/agentseam) ·
[context-report](https://github.com/open-coder-ai/context-report) ·
[threat intel](https://github.com/open-coder-ai/chock-threat-intel)

</div>

AI coding agents produce changes faster than people can review them, and the rules meant
to constrain them are prose: a line in `CLAUDE.md`, a `.cursorrules`, a
`copilot-instructions.md`. Prose is a suggestion. An agent can ignore it, and on an open
project a contributor brings an agent you never chose.

We build the control layer. Write a rule once, and a compiler turns it into the strongest
thing each agent actually supports: a git hook, a CI gate, a native pre-execution hook, or,
where nothing better exists, rule text labelled as advice. What is enforced and what is
only advised is derived from the repository and checked in CI, never typed into a README.

## The rule we hold ourselves to

> A rule an agent reads is advice. A hook that exits non-zero is a control. Both belong in
> a repo, and the difference has to be visible.

- **A claim must match a mechanism.** A policy that does not exit non-zero is advisory, and
  the tooling labels it that way whatever its manifest says.
- **Witnessed, not asserted.** An enforcement grade rises only after a real install was seen
  to block. Vendor documentation earns a lower grade than a live run, and the grade records
  which it was.
- **Guardrails, not guarantees.** Pattern-based guards can be evaded, `--no-verify` skips a
  git hook, and a fresh clone enforces nothing until it is wired. Every repo's `SECURITY.md`
  says so up front.

## What we build

| Project | What it is | Start |
| :--- | :--- | :--- |
| [**chock**](https://github.com/open-coder-ai/chock) | The framework. One policy becomes a git hook, a CI gate, native hooks in Claude Code, Cursor, Copilot CLI and VS Code, and an `AGENTS.md` for the rest — plus a coverage report that says where a guarantee holds. | `pip install chock` |
| [**chock-catalog**](https://github.com/open-coder-ai/chock-catalog) | The policies: secret scanning, protected branches, destructive-command guards, an OWASP Agentic Security pack. Each is labelled with what it actually reaches. | `chock add scan-secrets` |
| [**agentseam**](https://github.com/open-coder-ai/agentseam) | The primitives layer underneath: one handler API over every agent's hooks, instruction files, plugin packaging and config, with a capability matrix that carries its provenance. Stdlib only. | `pip install agentseam` |
| [**context-report**](https://github.com/open-coder-ai/context-report) | An open, signed report format for one question: does this plugin, hook, skill or `AGENTS.md` actually work? Reachability, fault behaviour, cost and efficacy, each row saying whether it is re-derivable or only claimed. | `pip install context-report` |
| [**chock-threat-intel**](https://github.com/open-coder-ai/chock-threat-intel) | A weekly, human-reviewed digest of agentic-AI threats, each scored against the catalog: enforced, advisory, or `policy wanted`. | [the ledger](https://github.com/open-coder-ai/chock-threat-intel/blob/main/reference/agentic-threat-ledger.md) |
| [**chock-claude-plugins**](https://github.com/open-coder-ai/chock-claude-plugins) · [copilot](https://github.com/open-coder-ai/chock-copilot-plugins) · [cursor](https://github.com/open-coder-ai/chock-cursor-plugins) · [codex](https://github.com/open-coder-ai/chock-codex-plugins) | The catalog compiled into each client's native plugin format. Generated only: CI rebuilds from source and fails on any difference. | add as a marketplace |
| [**chock-quickstart**](https://github.com/open-coder-ai/chock-quickstart) · [**chock-example**](https://github.com/open-coder-ai/chock-example) | Template repositories: exactly what `chock init` leaves behind, and a working adoption with one policy per layer. | *Use this template* |

## Two minutes to a blocked commit

```bash
pip install chock
chock init
chock add protect-main-branch
chock sync
git commit -m "straight to main"    # exits non-zero, whichever agent or human typed it
```

## Contribute — most of it needs no code

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
  [context-report](https://github.com/open-coder-ai/context-report/discussions).
  Telling us what is confusing is a contribution.
- **Bugs** → an issue on the repo that owns the source. The plugin repositories are
  compiled output; they point you to the catalog.
- **Vulnerabilities** → the private advisory linked from each repo's `SECURITY.md`, never a
  public issue.

<div align="center">

*Chock (rhymes with "block"): the wedge set against a wheel so it cannot roll until someone
deliberately removes it.*

</div>

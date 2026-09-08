# Contributing to open-coder-ai

This is the organisation-wide default. A repository with its own `CONTRIBUTING.md` overrides
it; read that one first. Where they exist, they are more specific and they win:
[chock](https://github.com/open-coder-ai/chock/blob/main/CONTRIBUTING.md),
[chock-catalog](https://github.com/open-coder-ai/chock-catalog/blob/main/CONTRIBUTING.md),
[agentseam](https://github.com/open-coder-ai/agentseam/blob/main/CONTRIBUTING.md),
[context-report](https://github.com/open-coder-ai/context-report/blob/main/CONTRIBUTING.md),
[chock-threat-intel](https://github.com/open-coder-ai/chock-threat-intel/blob/main/CONTRIBUTING.md).

## The rule every repository shares

**A claim must match a mechanism.** A rule an agent reads is advice; a hook that exits
non-zero is a control. Every repository here labels which one it is shipping, and a change
that widens a claim without widening the mechanism behind it is the one change that is never
merged. This applies to README text as much as to code.

## Where a contribution goes

| You want to | Go to |
| :--- | :--- |
| Add or correct a policy | [chock-catalog](https://github.com/open-coder-ai/chock-catalog). It reaches every client from there, including the four generated plugin distributions, which close hand-written pull requests |
| Report what an agent actually does when a hook denies, crashes or is missing | an [evidence report](https://github.com/open-coder-ai/agentseam/issues/new?template=evidence-report.yml) on agentseam. Most rows in its capability matrix rest on vendor documentation; a live run that contradicts one is the most useful thing you can send |
| Say a policy or a threat entry overstates itself | an issue on the repository that carries the claim. "This policy is wrong" is a welcome issue, not a rude one |
| Cover a published threat nothing enforces yet | claim a `policy wanted` entry in the [threat ledger](https://github.com/open-coder-ai/chock-threat-intel/blob/main/reference/agentic-threat-ledger.md) |
| Change the framework, the packaging or the emitters | [chock](https://github.com/open-coder-ai/chock) |
| Fix a README in a generated plugin repository | that repository; the README is its one hand-written file |
| Fix a demo repository | [chock](https://github.com/open-coder-ai/chock/issues/new/choose); the demos are exhibits of the framework's own output |

## Conventions that hold everywhere

- **Sign your commits.** `git commit -s` adds the Developer Certificate of Origin trailer.
  A commit without it does not merge.
- **Use an agent if you want to; keep its trailer.** Much of this organisation's code was
  written with a coding agent, and the `Co-Authored-By` trailers stay in the log. Every diff
  is read in full by a human before merge, agent-authored included, and you are the author:
  if you could not defend a line in review, take it out.
- **Describe the change, not the conversation.** Commit messages and pull request bodies say
  what changed and why. They do not narrate who asked, what was discussed, or carry links to
  a private session.
- **No hand-typed counts.** A number in a README is derived from the repository by a check,
  or it is not there. Prose counts go stale the week they are written.
- **Apache-2.0.** Every repository in the organisation. A contribution is licensed the same way.

## Reporting a vulnerability

Never in a public issue. See [SECURITY.md](SECURITY.md).

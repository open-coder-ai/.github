# Security Policy

This is the organisation-wide default, applied to any `open-coder-ai` repository that does
not carry its own `SECURITY.md`. The framework, the catalog, the primitives layer and the
report format each have their own, with supported versions, verification steps and a threat
model; read that one when it exists.

## Reporting a vulnerability

- **Never open a public issue** for an exploitable finding, in any repository here.
- **Open a private security advisory on the repository that owns the code.** For a finding
  in a generated plugin distribution, that is the framework, which emits the packages, or
  the catalog, which holds the policy source:
  - framework: <https://github.com/open-coder-ai/chock/security/advisories/new>
  - catalog: <https://github.com/open-coder-ai/chock-catalog/security/advisories/new>
  - primitives layer: <https://github.com/open-coder-ai/agentseam/security/advisories/new>
  - report format: <https://github.com/open-coder-ai/context-report/security/advisories/new>
- Include the affected artifact or tool path, reproduction steps, and the impact you
  believe it has.

Expect an acknowledgment within 7 days and an initial assessment within 14 days, the same
timelines the framework's own policy states. There is no bounty programme and no SLA beyond
that; this is open source maintained in the open.

## What these tools are, and are not

Every repository here says the same thing up front, and this file repeats it so the default
is never more reassuring than the specific: the guards are pattern-based filters and
**best-effort, not a security boundary**. Aliases, quoting and unusual paths can evade
them; `git commit --no-verify` skips every git hook; a fresh clone enforces nothing until
it is wired; a hook that cannot run allows on fail-open clients. A finding that shows one of
those limits is a known limit, stated in the owning repository's documentation. A finding
that shows a guard allowing what it claims to block, or a package differing from what the
catalog published, is a vulnerability, and the advisory route above is for it.

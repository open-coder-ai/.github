# .github

The organisation's profile page and the community files every `open-coder-ai` repository
falls back to when it does not carry its own.

| Path | What it is |
| :--- | :--- |
| `profile/README.md` | The page GitHub shows at https://github.com/open-coder-ai, above the repository list |
| `profile/org-mark.svg` | The organisation mark the page embeds |
| `CONTRIBUTING.md` | The rules shared by every repository, and where each kind of contribution goes |
| `SECURITY.md` | How to report a vulnerability privately, for repositories without their own policy |
| `SUPPORT.md` | Where questions and bugs go |
| `CODE_OF_CONDUCT.md` | Contributor Covenant, as every repository in the family adopts it |

GitHub applies `CONTRIBUTING.md`, `SECURITY.md`, `SUPPORT.md` and `CODE_OF_CONDUCT.md` to any
public repository in the organisation that lacks the file. A repository's own file always
wins, so the framework, catalog, primitives layer and report format keep their more specific
guides; the defaults here serve the demo repositories, the threat-intel digest and the four
generated plugin distributions.

The profile page states no count by hand, for the same reason the repositories' social cards
do not: a number typed into a profile is stale the week after. The source of this page and
the brand it draws on live in the organisation's planning repository.

Apache-2.0 — see [LICENSE](LICENSE).

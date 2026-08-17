# Migration Notes

## v1.1.0 — core skills renumbered

The three core skills were renamed to restore parity with upstream TRIP, which
ships `TRIP-1-plan` / `TRIP-2-implement` / `TRIP-3-release`. The numbers were
dropped during the original sanitization; this restores them.

| Old (v1.0.0)     | New (v1.1.0)       |
| ---------------- | ------------------ |
| `garm-plan`      | `garm-1-plan`      |
| `garm-implement` | `garm-2-implement` |
| `garm-release`   | `garm-3-release`   |

All other skills keep their names. `garm-init`, `garm-compact`, `garm-research`,
`garm-review`, and `garm-test` are unnumbered upstream as well.

### Impact on existing installs

This repository is a **template**. Projects already bootstrapped by `garm-init`
have their own customized copies under `.claude/skills/`, which this rename does
not touch — they keep working under the old names.

When `garm-upgrade` is reintroduced, it must carry a rename-mapping table so
installs on the old folder names merge into the new ones rather than being
treated as unrelated skills. Upstream solves the identical problem in
`skills/TRIP-upgrade/SKILL.md` (see its "Renamed in TRIP v2" section, which maps
`TRIP-3-review` → `TRIP-review` and `TRIP-4-test` → `TRIP-test`). Model the GARM
table on that: merge the old folder's project customizations into the new name,
then delete the old folder.

## v1.1.0 — skills temporarily withheld

`garm-hotfix`, `garm-upgrade`, and the top-level `AskUserQuestion/` shim were
removed to keep the onboarding surface small. None had inbound dependencies, so
nothing else changed to accommodate their removal.

`garm-compact` was **kept** — `garm-3-release` Step 7 calls its
`count-tokens.sh` to guard MIMIR.md size.

The `AskUserQuestion` prose instructions inside the retained skills were left in
place intentionally: Claude Code answers them with its native tool, and the shim
only ever existed for agents lacking one.

Recover any of the three from git history:

```
git log --diff-filter=D --name-only -- skills/garm-hotfix
git checkout <commit>^ -- skills/garm-hotfix
```

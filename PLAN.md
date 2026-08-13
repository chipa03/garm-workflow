# Build GARM — a sanitized, ground-up clone of the TRIP workflow

## Context

GARM is a multi-model development workflow built with heavy inspiration from TRIP (`d:\repos\TRIP-workflow`). GARM must behave **exactly** like TRIP — same skills, same phases, same Codex integration, same init/upgrade mechanics — but with all TRIP branding removed: skills renamed to `garm-*`, `ARCHI.md` renamed to `MIMIR.md`, and every internal cross-reference updated. Copying skill content verbatim is explicitly allowed where functionality requires it.

**Decisions confirmed:**
- GARM lives in a **new sibling repo**: `d:\repos\GARM-workflow` (fresh `git init`; TRIP-workflow untouched).
- All TRIP skills become lowercase `garm-*` with **numbers dropped** (per the given examples `TRIP-1-plan → garm-plan`, `TRIP-2-implement → garm-implement`).
- The 4 `codex-*` helper skills **keep their names**; only internal references change.
- The 3 TRIP-branded PNG assets are **dropped**; diagrams become mermaid blocks in the README.

## Rename mapping

### Directories / files

| TRIP | GARM |
|---|---|
| `skills/TRIP-1-plan/` | `skills/garm-plan/` |
| `skills/TRIP-2-implement/` | `skills/garm-implement/` |
| `skills/TRIP-3-release/` | `skills/garm-release/` |
| `skills/TRIP-review/` | `skills/garm-review/` |
| `skills/TRIP-test/` | `skills/garm-test/` |
| `skills/TRIP-init/` | `skills/garm-init/` |
| `skills/TRIP-upgrade/` | `skills/garm-upgrade/` |
| `skills/TRIP-hotfix/` | `skills/garm-hotfix/` |
| `skills/TRIP-research/` | `skills/garm-research/` |
| `skills/TRIP-compact/` | `skills/garm-compact/` |
| `skills/codex-{plan-review,code-review,implement,ask}/` | unchanged |
| `AskUserQuestion/` (repo root) | unchanged (stays at root — README tells Mistral users to copy it separately) |
| `assets/*.png` (3 files) | **not copied** |
| `.gitignore` (content: `.claude/`) | copied as-is |

### Strings (inside files)

| TRIP token | GARM token |
|---|---|
| `docs/ARCHI.md`, `ARCHI.md` | `docs/MIMIR.md`, `MIMIR.md` |
| `ARCHI-rules.md` | `MIMIR-rules.md` |
| `ARCHI-compact.md` (count-tokens.sh usage comment) | `MIMIR-compact.md` |
| bare `ARCHI` (e.g. `[From ARCHI: …]`, "actual ARCHI sections") | `MIMIR` |
| `.claude/skills/TRIP-<x>/…` install paths | `.claude/skills/garm-<x>/…` |
| `new-TRIP/` staging folder (garm-upgrade) | `new-GARM/` |
| `/TRIP-1-plan`, `TRIP-1` etc. slash-command & shorthand refs | `/garm-plan`, `garm-plan` (numbers gone — shorthands like "TRIP-2 Testing Gate" become "garm-implement Testing Gate") |
| frontmatter `name: TRIP-*` + descriptions mentioning TRIP | `name: garm-*`, GARM wording |
| Workflow version `2.6.0` | GARM starts at **`1.0.0`** |

**Do NOT change (false positives / intentional keeps):**
- `tripwire` (`TRIP-2-implement/SKILL.md:129`) and "trip" as an English word (`TRIP-2-implement:104-105`, `TRIP-3-release:70`).
- Model codenames `Luna` / `Sol` / `Fable`, `gpt-5.6-luna` / `gpt-5.6-sol` in `codex-plan-review/scripts/_common.sh` and the 3 codex SKILL.md files — these are model defaults, not TRIP branding. Functionality requires them verbatim.
- All placeholder tokens (`[PROJECT_NAME]`, `[ADAPT_TO_PROJECT…]`, `[VERSION_FILE]`, `[WEEK_ANCHOR_DATE]`, `[MAIN_BRANCH]`, `[TUTORIAL_STEP]`, `[LINT_COMMAND]`, `[TYPECHECK_COMMAND]`, `[TEST_COMMAND*]`, etc.).
- The generated `docs/` tree names (`docs/1-plans/`, `docs/2-changelog/`, `docs/3-code-review/`, `docs/4-unit-tests/`, `docs/5-tuto/`, `docs/6-memo/`, `CR_wa_vx.y.z.md`) — not TRIP-branded.
- Third-party names in README (Claude Code, Codex CLI, OpenCode, Mistral Vibe, Context7, Exa, Superpowers, BMAD, Gastown, agentskills.io).

## Implementation steps

### 1. Scaffold the new repo
- Create `d:\repos\GARM-workflow`, `git init`.
- Copy everything from TRIP-workflow **except** `.git/`, `assets/`, and `README.md` (rewritten fresh in step 4), renaming the 10 `TRIP-*` skill directories per the mapping.

### 2. Mechanical string pass (scripted, exact tokens only)
Run ordered exact-token replacements across all copied text files (longest-first to avoid partial hits): each `TRIP-<x>` skill name → `garm-<x>`, `new-TRIP` → `new-GARM`, `ARCHI-rules` → `MIMIR-rules`, `ARCHI-compact` → `MIMIR-compact`, `ARCHI.md` → `MIMIR.md`, then remaining `ARCHI` → `MIMIR`. **Never** blanket-replace `TRIP` — after the token pass, grep for leftover `TRIP` and resolve each hit manually (expected leftovers: prose like "TRIP workflow", "TRIP skills", headers, plus the protected `tripwire`/"trip" words).

Files known to carry rename-sensitive strings (from the full grep inventory):
- All 10 renamed skills' `SKILL.md` (heaviest: `TRIP-init` 49×TRIP + 44×ARCHI; `TRIP-upgrade` 52×TRIP).
- `TRIP-review/checklist.md`, `TRIP-review/cr-template.md` (self-referencing `.claude/skills/TRIP-review/…` paths).
- `TRIP-compact/count-tokens.sh` (1 ARCHI ref — the **only** shell script touched).
- Easy-to-miss, under non-TRIP dirs: `codex-code-review/prompts/{start,resume,synthesize}.tpl` (TRIP-review paths + ARCHI), `codex-code-review/SKILL.md`, `codex-implement/SKILL.md`, `codex-implement/prompts/implement.tpl` (ARCHI ×2), `codex-plan-review/prompts/start.tpl` (ARCHI ×1), `codex-ask/prompts/ask.tpl` (ARCHI ×1).
- Verbatim, zero changes: `AskUserQuestion/SKILL.md`, all `codex-plan-review/scripts/*.sh`, `codex-implement/scripts/start.sh`, `codex-ask/prompts/followup.tpl`, both `state/.gitignore` files, root `.gitignore`.

### 3. Manual prose pass (per file)
- Frontmatter: `name:` must equal the new directory name; `description:` reworded (e.g. `garm-init`: "Initialize GARM workflow in a new project (creates docs structure and generates MIMIR.md)").
- Headers/prose: "# TRIP Initialization Mode" → "# GARM Initialization Mode", "the TRIP workflow" → "the GARM workflow", etc.
- `garm-init` "What is TRIP?" intro (lines 12-21): keep the four-phase Plan/Implement/Review/Test framing identical; replace the reversed-acronym joke ("Why call it TRIP instead of PIRT?") with GARM-appropriate flavor (GARM/MIMIR are Norse-themed — Mímir being the keeper of memory fits the architecture-memory file; keep it one line, same tone).
- Ordinal shorthands: "TRIP-2 Testing Gate" → "garm-implement Testing Gate", "TRIP-3 Documentation Sync step" → "garm-release Documentation Sync step" (in `garm-init` lines ~629, 740 and anywhere else grep finds `TRIP-\d`).
- "Happy tripping ! 🍄" (if reused anywhere) → GARM-flavored sign-off, e.g. "Happy hunting ! 🐺".

### 4. Rewrite README.md from scratch (GARM edition)
Same structure and content as TRIP's README, sanitized:
- Title line `# GARM Workflow` instead of the banner PNG; drop the loop PNG (replace with a small mermaid `flowchart LR` of `/garm-plan → /garm-implement → /garm-release` looping back) and drop the multiLLM PNG (the detailed mermaid flowchart at the bottom already covers it — update its node labels to `/garm-plan`, `/garm-implement`, `/garm-release`).
- Badges: `version-1.0.0`; drop the stale MIT/LICENSE badge (it points at `PiLastDigit/TRIP-workflow` and no LICENSE file exists); keep the tool-compat badges (Claude Code, Codex CLI, OpenCode, Mistral Vibe).
- Drop the TRIP demo-video link (`user-attachments` URL at line 52).
- Rewrite version-history sentences without TRIP history: "Since v2.0.0 the flow is even simpler…" → describe the current Plan → Implement → Release flow directly; "As of v2.0.0, this multi-agent approach is the default" → "This multi-agent approach is the default workflow."
- "3 numbered skills. 1 architecture file." → "3 core skills. 1 architecture file." and "If you can count to 3, you can TRIP" → equivalent GARM line (numbers are gone from names but the flow order stays explicit in the table: `/garm-plan` → `/garm-implement` → `/garm-release`).
- "The Heart of GARM: MIMIR.md" section — same content, MIMIR naming (`docs/MIMIR.md`).
- "More Skills" blurbs for `/garm-review`, `/garm-test`, `/garm-upgrade` (staging folder `new-GARM/`), `/garm-hotfix`, `/garm-research`, `/garm-compact`, and the codex skills — same one-liners, renamed.
- Getting Started: copy `skills/` → `.claude/skills/`, run `/garm-init [YourProjectName]`, approve generated MIMIR.md; keep the Mistral/AskUserQuestion note.
- Keep the Why/MCP/Contributing sections' substance; closing line gets the GARM sign-off.

### 5. Adapt `garm-upgrade` (the one skill where pure token-renaming is insufficient)
It contains TRIP's own version history, which never existed for GARM (GARM ships as v1.0.0):
- `argument-hint` and staging path: `[path to new-GARM folder]`, `.claude/skills/new-GARM/`.
- Skill category lists renamed: pure-workflow = `garm-compact, garm-hotfix, garm-research, garm-init, codex-*`; customized = `garm-plan, garm-implement, garm-release, garm-review, garm-test`. Keep the `_common.sh` model-defaults exception verbatim.
- **Remove** TRIP-generation-specific content that can never occur in a GARM install: the "Renamed in TRIP v2" table (`TRIP-3-review→TRIP-review`, `TRIP-4-test→TRIP-test`), the "`TRIP-3-release` is new in v2, values extracted from old TRIP-2" rule, all "in v1 installs…" parentheticals (Phases 2.2, 3.1–3.3, Edge Cases), and the "(may not exist in pre-v2.5 installs)" note → "(may not exist in older installs)".
- **Keep the mechanisms generic** so future GARM versions can use them: Phase 3 becomes a short "Handle Structural Migrations" section stating that if a newer GARM version renames a skill or moves content between files, treat old/new as the same skill and migrate extracted content accordingly (the machinery — inventory table, extract → merge → replace, validation — stays intact).
- Phase 1.3 example inventory table rewritten with `garm-*` names and statuses that make sense for a v1→v1.x upgrade.
- Phase 5.1 validation grep glob: `.claude/skills/TRIP-*/` → `.claude/skills/garm-*/`; Phase 5.2 cross-reference paths → `garm-review/checklist.md`, `garm-review/cr-template.md`, etc.

### 6. Consistency details
- `garm-release` line 113: `@docs/ARCHI-rules.md` → `@docs/MIMIR-rules.md`; line 115 script path → `.claude/skills/garm-compact/count-tokens.sh docs/MIMIR.md` (same in `garm-compact/SKILL.md:31,186`).
- `garm-implement` line ~210: read-and-follow path → `.claude/skills/garm-release/SKILL.md`.
- `garm-init` Phase 6 checklist (lines ~1036-1058) and Phase 6.x headings: every `TRIP-1/2/3/review/test` reference → the new names; generated-files list → `docs/MIMIR.md, docs/MIMIR-rules.md, …`; changelog seed row "chore: initialize TRIP workflow" → "…GARM workflow".
- `codex-code-review` prompts: `.claude/skills/TRIP-review/checklist.md|cr-template.md|SKILL.md` → `garm-review/…` (including the "do NOT read" negative references in `start.tpl`/`resume.tpl`).

## Verification

Run from `d:\repos\GARM-workflow`:
1. `grep -rn "ARCHI" .` → **zero** hits.
2. `grep -rni "trip" . --exclude-dir=.git` → only the whitelisted English words: `tripwire` (garm-implement), "trip"/"round-trip"-style prose (garm-implement, garm-release). Nothing skill-name-shaped, nothing in README.
3. `grep -rn "TRIP-\|new-TRIP\|/TRIP" .` → zero.
4. Frontmatter check: every `skills/*/SKILL.md` `name:` equals its directory name; `AskUserQuestion` unchanged.
5. Cross-reference integrity: every `.claude/skills/<name>/` literal mentioned in any file points at a directory that exists in `skills/` (spot-check the 6 known path clusters: garm-review/checklist.md, garm-review/cr-template.md, garm-release/SKILL.md, garm-compact/count-tokens.sh, codex-plan-review/scripts/*.sh, codex-implement/scripts/start.sh).
6. Functional parity diff: for each renamed skill, `git diff --no-index` its SKILL.md against the TRIP original and confirm every hunk is naming/branding only — no step, command, phase, gate, or placeholder was added or removed (garm-upgrade is the sanctioned exception per step 5, README per step 4).
7. Byte-identical check: `_common.sh`, the 6 other `.sh` scripts (except count-tokens.sh's one comment line), `AskUserQuestion/SKILL.md`, `followup.tpl`, and the `state/.gitignore` files must be identical to TRIP's.
8. Mermaid render check: paste the README's mermaid blocks into a renderer (or rely on GitHub preview) to confirm they parse.
9. Initial commit on the new repo once all checks pass.

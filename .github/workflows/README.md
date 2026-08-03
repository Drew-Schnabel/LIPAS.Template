# Shared PR-governance workflows

The estate-wide PR-governance set, carried by this template so every repo generated from it starts
with identical PR hygiene. `LIPAS.SDK` remains the source of truth — change a workflow there first,
then re-mirror into this template and the downstream repos.

These govern the PR lifecycle only. Build, test, packaging and release are each repo's own concern;
the SDK's build pipeline is bound to its project layout and secrets and is deliberately not carried
here.

| File | Trigger | Effect |
| --- | --- | --- |
| `bot-pr-triage.yml` | PR opened/reopened by a bot | Labels `ai-generated` + `needs-review`, posts a review checklist |
| `pr-size-guard.yml` | PR opened/synchronized | Labels `size/large` and warns past 40 files / 800 lines |
| `sensitive-path.yml` | PR opened/synchronized | Labels `needs-careful-review` when CI, auth, lockfiles or migrations are touched |
| `changes-requested.yml` | Review requests changes | Assigns the author, labels `Changes Requested By Reviewer`, clears the reviewer |
| `ready-for-re-review.yml` | Push to a changes-requested PR | Flips back to `needs-review`, re-requests the original reviewers |
| `auto-merge.yml` | Review submitted / PR labeled | Enables native auto-merge on approved PRs carrying `automerge` |
| `backport.yml` | Merged PR labeled `backport <branch>` | Opens a cherry-pick PR against that release line |
| `release-drafter.yml` | Push to `master` / `release-v**`, PR activity | Maintains a draft release from merged PR titles; autolabels PRs |
| `lint-workflows.yml` | PR or push touching `.github/**` | Runs `actionlint` over every workflow |
| `enforce-merge-progress.yml` | PR into a release line | Gates `feature/*` → `pre-release-v*` → `release-v*` → `master`. **Opt-in, see below** |

Config lives alongside: `.github/release-drafter.yml` (categories, version resolver) and
`.github/dependabot.yml` (weekly action SHA bumps).

## Checklist for a repo generated from this template

1. **Allow auto-merge** — Settings → General. Without it `auto-merge.yml` logs and exits.
2. **Copy the labels.** GitHub does *not* carry labels through template generation; only files and
   branches are copied. Mirror them from `LIPAS.SDK` before the workflows start applying them, or
   the first PR will auto-create them with arbitrary grey colours.
3. **Enable the merge-flow gate when ready** — `gh variable set ENFORCE_MERGE_FLOW --body true`,
   once `pre-release-v*` / `release-v*` lines actually exist. Leaving it unset keeps the gate
   dormant so early feature-to-`master` PRs are not blocked.
4. **Optional:** set `CONTENTS_TOKEN` if backports need to bypass branch protection; otherwise
   `backport.yml` falls back to the ephemeral `GITHUB_TOKEN`.

## Labels these depend on

`ai-generated`, `needs-review`, `needs-careful-review`, `size/large`,
`Changes Requested By Reviewer`, plus `automerge` and `backport <branch>` applied by hand to opt a
PR into those flows. Missing labels are auto-created on first use with a default colour, so seeding
them from `LIPAS.SDK` is worth doing up front.

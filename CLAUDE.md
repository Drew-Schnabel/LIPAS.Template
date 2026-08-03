# <REPO NAME> — Claude Code instructions

<!-- Template. Replace <REPO NAME> and every PER-REPO block below, then delete
     the HTML comments. Sections marked SHARED are estate-wide — keep them
     verbatim so they stay diffable across repos, and change them in
     LIPAS.Template first. -->

## Vault context

Drew's LIPAS Obsidian vault: `~/Documents/Obsidian/Vault/LIPAS Projects`

This repo's notes:
<!-- PER-REPO: list the hub note, ADRs and patterns that touch this repo. -->
- `40 - Projects/<REPO NAME>.md` — project hub
- ADRs touching this project:
  - `80 - Decisions/<decision>.md`
- Patterns:
  - `50 - Patterns/<pattern>.md`

When you need context, read these notes first. Wikilinks resolve to other vault files — follow them when relevant.

## Vault updates: invoke the curator at recap

<!-- SHARED — keep verbatim. -->

Drew uses an [[End-of-Session Feedback Loop]] ritual. At recap time:

1. Invoke the **`lipas-vault-curator`** subagent to produce a "Proposed vault updates" block.
2. Surface it alongside any "Proposed memory edits."
3. Drew approves per item.
4. Apply the approved edits via filesystem (or Obsidian MCP when available).

The curator is read-only by design (`tools: Read, Glob, Grep`) — it proposes, the main thread applies. Conventions (frontmatter shape, ADR filename pattern, naming, wikilink format) live inside the agent's prompt.

## When the curator should be invoked

<!-- SHARED triggers. Append PER-REPO triggers to the list; do not remove the shared ones. -->

Whenever this session produced any of:

- Architecture change (new component, removed component, changed boundary, new dependency, new context)
- Convention change (new anti-pattern, naming rule, pipeline shape)
- Durable decision with operational consequences and considered alternatives → ADR candidate
- Configuration surface change (new options key, default change, CLI flag, feature flag) → runbook update
- Observability change (new log enrichment field, new OTel tag, new alert)
- Public-API surface change, if this repo publishes a package others bind to

Bug fixes, behavior-neutral refactors, and one-off ops tasks do NOT warrant invoking the curator.

## What NOT to write to the vault

<!-- SHARED — keep verbatim. -->

- Specific colleague names, internal URLs, server names, ATO identifiers, TRM-approved version numbers, sensitive data — see vault `README.md`.
- Edits Drew has not approved per item in the recap.
- Mid-session writes — vault edits flow through the recap gate, never silently.

## Working agreements

<!-- SHARED — keep verbatim. -->

- **Branch flow is `feature/*` → `pre-release-v*` → `release-v*` → `master`.** Never open a PR that
  skips a stage. Where `ENFORCE_MERGE_FLOW` is set, CI rejects it; where it is not, the rule still
  holds.
- **Documentation ships in the same PR as the code.** A doc gap is a merge blocker, not a follow-up —
  see the "Documentation ships with the change" section of the global `~/.claude/CLAUDE.md` for what
  that requires.
- **Reasoning belongs in the ADR or rule doc, never in a code comment.** Comments stay terse and
  code-related: no issue numbers, no provenance, no narrative about what was tried.
- **Every inline comment on a PR gets a reply before that review is done.** Answering in chat is not
  answering the review.
- **A finding that needs re-confirmation goes on the GitHub issue** — comment, `SME Input Needed`
  label, assign the decider. A conclusion reached only in chat has not been recorded.
- **Run a senior-engineer review before opening the PR**, and verify what it claims rather than
  applying it wholesale.
- **Don't delete a config block that has gone dead.** Install the missing dependency and leave the
  block primed to flip; confirm before removing anything.

## Code conventions (enforced)

<!-- SHARED — keep verbatim. Applies estate-wide: every repo binds to LIPAS.SDK.Domain. -->

- **Always prefer domain value objects over base types.** Model and contract members must use the
  `LIPAS.SDK.Domain` value objects rather than primitives: `PolicyNumber` (not `string`),
  `MonetaryAmount` (not `decimal` for dollar amounts), `EffectiveDate` / `PaidToDate` /
  `PaymentDate` (not `int` YYYYMMDD or `DateTime` for those domain dates), `SocialSecurityNumber`,
  `NameId`, `LifeProCoderId`, `TransactionId`, `BatchId`, `EmailAddress`, `PhoneNumber`, `Address`,
  `AccountingControlNumber`. This applies to service method signatures, request/response DTOs, and
  verdict/composite models alike. Each value object ships a `[JsonConverter]` that serializes to the
  bare primitive, so the JSON wire shape is unchanged — there is no serialization cost to adopting
  them. Non-domain decimals that are *not* money (e.g. an interest rate), counts, and day-offset
  integers stay as primitives. Reviewers will request changes when a primitive is used where a value
  object exists.

## This repo

<!-- PER-REPO: what this repo is, its layers, where tests live, what it publishes
     or consumes, and anything that cascades when it changes. -->

- **<one line: what this repo is>**
- Layers / projects: <...>
- Tests in `<path>`.
- See `README.md` and the vault hub for architecture; see [[Code Standards (.NET)]] for code conventions.

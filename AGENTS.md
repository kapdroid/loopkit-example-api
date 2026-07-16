# AGENTS — loopkit-example-api

> Rendered by `kap adopt`/`kap init` from `kernel/AGENTS.template.md`. This is the open, tool-agnostic
> baseline every agent (Claude Code, Codex, Cursor, opencode, or a human) reads. Tool-specific dirs
> (`.claude/` etc.) are **generated from this file** and only augment it — nothing load-bearing lives
> only there.

**Stack:** `rust` · **Tier:** `S` · **Trunk:** `main`

## The one governing rule

Can a machine decide it? **Yes → gate it. No → prompt for the first draft, review for the catch.**
Evidence over assertion: "tests green @ `<sha>`", never "I implemented it". Read real files and
contracts before you write — never recall an API. Full rationale: `kernel/CONVENTIONS.md`.

## The loop (the only commands you need)

```
kap start <bead>              # fork a worktree lane off fresh trunk, claim the bead, pre-install deps
cargo clippy --all-targets -- -D warnings                 # (= kap check) fast, lock-free edit loop — run this constantly
kap done <bead> "<evidence>"  # the close gate: cargo fmt --check && cargo clippy --all-targets -- -D warnings && cargo test on diff-vs-trunk + guards; closes on green
kap merge <bead>              # (lead only) serialized integration under the trunk lock
```

- Gate (the definition of done for this stack): `cargo fmt --check && cargo clippy --all-targets -- -D warnings && cargo test`
- Fast check: `cargo clippy --all-targets -- -D warnings`
- Format: `cargo fmt -- {file}`

## Common mistakes — never do these

- **Never edit in the main checkout.** All file-changing work is in a `kap start` lane. (Invariant 2)
- **Never use the full close gate as your edit loop** — that serializes on the gate lock and starves
  every other lane. `kap check` is the inner loop; `kap done` runs once at the end. (Invariant 3)
- **Never push to the trunk branch or self-authorize an irreversible action** (deploy/delete/publish/
  external message). Those are deploy-lane beads, born blocked, awaiting a human. (Invariant 5)
- **Never close a bead without evidence** — a gate result + SHA in the close reason, not a bare flip.
- **Never absorb a mid-work discovery into the current bead** — file it as a new bead linked
  `discovered-from`. (Invariant 1)

## Work shape

One bead = one reviewable PR against trunk. Bigger → an epic of per-PR beads (tree, ≤2 levels).
Smaller → checklist lines inside the description. `blocks` means a hard prerequisite and nothing else.

## Where things are

- `PLAN.md` (if present) — this repo's goal + plan + Definitions of Done.
- `kernel/CONVENTIONS.md` — the six invariants (the *why* behind the loop).
- `adapters/rust/` — the `rust` stack contract (gate, check, fmt, rules).
- `docs/decisions/` — the ADRs (why the structural choices were made).

## Rules for this stack

The universal `rust` rules live in `adapters/rust/rules.md` (read at session start); the
task-specific bar lives in each bead's design brief + `acceptance-check`. The deterministic spine
(the `[GATE]` rules) is `cargo clippy --all-targets -- -D warnings`; judgment rules are the per-stack reviewer, advisory.
# Tasks

One task per file, named `NNN-slug.md`. `000-template.md` is the template. Task
cards are owned by the lead and are the only brief an implementer receives.

## Status

```
proposed → ready → in-progress → verify → review → done
                       │            │        │
                       └────────────┴────────┴──→ blocked
```

- **proposed** — the lead has drafted the card; scope may still change.
- **ready** — the contract is settled and the card is dispatchable.
- **in-progress** — one implementer session owns the branch.
- **verify** — the verifier is running the acceptance commands.
- **review** — the reviewer is reading the diff.
- **done** — verified, reviewed, merged, and recorded in `docs/status.md`.
- **blocked** — record the exact blocker and who must resolve it.

## Rules

- One deliverable, one owner, one branch.
- The task ID is the file stem, for example `003-pci-detector`. The slash
  commands accept that stem, not just the number.
- Every card lists allowed files, forbidden files, the interface contract, exact
  acceptance commands, verified facts, and known risks.
- The implementer never edits its own card. If the card is wrong, it reports
  back; the lead fixes the card.
- A dependency addition needs an ADR and human approval before it appears in a
  card's acceptance commands.
- Split a task that exceeds roughly 400 changed lines or touches two crates.
- A card is not done because the code compiles. The verifier must report PASS and
  the reviewer must have no blocking findings.

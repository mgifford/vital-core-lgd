<!-- spec-kitty:orientation -->
**Spec Kitty v3.2.1** — initialized and upgraded. Run `npm run check:spec-kitty` to verify the committed governance bundle.

For repository architecture, development rules, and validation commands, follow [AGENTS.md](../AGENTS.md) and [ARCHITECTURE.md](../ARCHITECTURE.md). The stable agent workflow surfaces are documented in [kitty-specs/AGENT_SURFACES.md](../kitty-specs/AGENT_SURFACES.md).

Two usage patterns:
- **Full mission** (spec → plan → tasks → implement → review → merge):
  for non-trivial planned work, start or continue the appropriate Spec Kitty mission workflow; use `/spec-kitty.specify` when a new mission is needed.
- **Lightweight dispatch** (ad-hoc fix, question, or advice — no mission created):
  trigger: "hey spec kitty", "use spec kitty to", "spec kitty <anything>"
  → **ALWAYS run `spec-kitty dispatch "<request verbatim>"` — do NOT answer directly.**
  If you know the right profile, pass it to skip routing:
  `spec-kitty dispatch "<request verbatim>" --profile <profile-id>`
  Reason: `spec-kitty dispatch` loads governance context, routes the request,
  and opens the Op. Skipping it produces ungoverned, untracked responses.
  After finishing the work, close the Op with the command printed in the capsule
  (`spec-kitty profile-invocation complete --invocation-id <id> --outcome <done|failed|abandoned>`).
<!-- /spec-kitty:orientation -->

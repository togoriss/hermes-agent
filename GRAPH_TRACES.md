# Hermes Agent — Graph Traces (to explore)

Knowledge graph built with graphify on the **core code** scope
(root `.py` files + `agent/`, `gateway/`, `tools/`, `providers/`, `hermes_cli/`
+ key docs). Graph: **19,683 nodes · 52,059 edges · 462 communities**.

Artifacts live in `graphify-out/` (gitignored — regenerated locally by the
post-commit hook). Open `graphify-out/graph.html` for the interactive map,
`graphify-out/GRAPH_REPORT.md` for the full audit.

Run a trace with: `graphify query "<question>"`

## Traces to do (priority order)

- [ ] **1. Config coupling spine** — `load_config()` (327 edges, betweenness 0.142)
  and `get_hermes_home()` (290 edges, betweenness 0.154) bridge ~90 communities
  each. Trace how config + the `~/.hermes` home dir propagate from the CLI/secrets
  layer out into gateway, agent, tools, and MCP. This is the single biggest
  coupling point in the codebase.

- [ ] **2. Agent core loop** — hyperedge *"Agent Core Loop and Tool Dispatch Flow"*:
  `AIAgent` → `agent_loop` → `model_tools` → `tool_registry` → `toolsets`.
  Trace one user turn end-to-end through tool dispatch.

- [ ] **3. Closed learning loop** — hyperedge *"Closed Learning Loop"*:
  `learning_loop` → `skills` → `curator` → `memory_manager` → `session_db`.
  How experience becomes skills/memory and gets recalled across sessions.

- [ ] **4. Platform / gateway architecture** — `Platform` (382 edges, the #1 god
  node), `PlatformConfig`, `GatewayRunner`, `BasePlatformAdapter`, and the
  per-platform adapters (Feishu, Telegram, Discord, QQ...). How a message enters,
  gets authorized, and routed. (Largest subsystem — communities 0,1,2,4,11,14.)

- [ ] **5. Secret redaction path** — `RedactingFormatter` (`agent/redact.py`) is
  pulled into `Filter`/`Formatter`/`Logger` in `hermes_logging.py`. Confirm secrets
  are redacted across every log surface.

- [ ] **6. Security / trust model** — approval gate, OS-isolation boundary,
  in-process heuristics, credential scoping (from `SECURITY.md`). Where is the
  command-approval gate enforced and what can bypass it?

- [ ] **7. Context engine & compression** — `ContextEngine.compress` /
  `should_compress` (community 9). When/how context gets compressed mid-session.

- [ ] **8. Session persistence** — `SessionDB` (268 edges) across insights,
  gateway, and agent. The FTS5 session search + cross-session recall path.

## Open verification questions (flagged by the graph)

- [ ] `Platform` has **329 INFERRED edges** — model-reasoned, need spot-checking.
- [ ] `AIAgent` has **49 INFERRED edges** — verify the non-obvious ones.
- [ ] Duplicate `Skill Authoring Standards (HARDLINE)` node with an AMBIGUOUS
  self-reference — likely a doc artifact to clean up.

## Notes

- Scope deliberately excluded `tests/`, `website/`, `locales/`, `apps/`,
  `ui-tui/`, `optional-skills/` to keep signal high. Re-run with a wider scope
  if those subsystems become relevant.
- Re-extract after big changes: `graphify <path> --update` (or just commit — the
  post-commit hook handles it).

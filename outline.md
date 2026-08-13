# The Context Window Ate My Steering File
### (dilution is the slope, compaction is the cliff) — timed study outline with annotated references

**Thesis:** directive files enter the context deterministically, then lose influence as the loop appends tool output (*dilution*) — well before lossy summarization (*compaction*). You can't stop the physics, but you can manage the log from five sides — and decide, deliberately, where each piece of knowledge deserves to live (the promotion ladder).

**Evidence tiers:** [official] vendor docs/blog · [study] arXiv controlled experiments · [community] unofficial (say so aloud) · [mine] my canary log

---

## 0:00–0:40 · Hook

- One-breath Replit reference: "You've heard about the agent that deleted a production database through a code freeze. The instructions were in the file. The file was in the context. The context didn't win."
  — [public incident] Lemkin/Replit, Jul 2025: day 9 of extended use; replit.md required explicit permission; "NO MORE CHANGES" freeze; live records wiped (~1,200 execs / ~1,190 companies); CEO: "unacceptable and should never be possible"; shipped fix = deterministic dev/prod separation. theregister.com/2025/07/21/replit_saastr_vibe_coding_incident · incidentdatabase.ai/cite/1152
  — *Q&A note:* proves directives are context-not-enforcement; does NOT prove dilution caused it (no token data).
- Show of hands: "Who has a CLAUDE.md or steering file? Keep it up if the agent's ignored one."

## 0:40–1:25 · Mechanism: the loop is an append-only log

- Agent = LLM + tools in a loop; every iteration **appends**. Directives injected **once, early**: CLAUDE.md files concatenated root→cwd, delivered as a **user message after the system prompt**. — [official] code.claude.com/docs/en/memory
- Attention = **finite budget, diminishing returns** (n tokens ⇒ n² pairwise relationships). — [official] Anthropic, *Effective context engineering for AI agents*
- **Tool observations: 70–80% of the token budget** in ReAct-style loops. Your rules become row two of a very long table. — [study] arXiv:2603.05344 §2.3.6
- On-title micro-anecdote (15s): Kiro teaching steering file, `inclusion: always`, "do NOT create files for the student" — overridden by generic task instructions. Selection worked; adherence lost. — [community] kirodotdev/Kiro issue #2250 (verify firsthand)

## 1:25–2:05 · The measured decay (dilution — the slope)

- Compliance ~**60–68%** across file sizes (25–500 lines) and positions; 50 runs/condition; AST-verified; **no compaction involved**. — [study] arXiv:2605.10039
- Within-session decay: **>95% early → 20–60% by messages 6–10**. — [community] S. Khare
- Vendors concede: **>200 lines may reduce adherence**; conflicts picked **arbitrarily**. — [official] code.claude.com/docs/en/memory
- Ceiling arrives early: correctness falls ~**32k tokens** (Llama 3.1 405b; earlier for smaller); Gemini 2.5 agents past ~**100k** repeat history instead of planning. — [community/industry] Databricks study + Gemini 2.5 report, via D. Breunig, *How Long Contexts Fail* — name his taxonomy once: **poisoning · distraction · confusion · clash**
- Framing line: "**Dilution is the slope; compaction is the cliff.** All of this happens on the slope, while the gauge still looks green."

## 2:05–2:40 · The gauges (and their blind spot)

- Kiro IDE: **context usage meter**; **auto-summarize at 80%**. — [official] kiro.dev/docs/chat/summarization
- Kiro CLI: **`/context`** = % + category breakdown (context files / tools / responses / prompts); **`/context show`** = per-file share. — [official] kiro.dev/docs/cli/chat/context
- Demo or screenshot: "context files: 0.3%. Everything else: the rest."
- Blind spot: gauges measure distance to **compaction**, not **degradation** — and degradation starts first. No vendor publishes an accuracy threshold.

## 2:40–3:20 · The canary — and my null result [mine]

- Protocol: **plant** one cheap verifiable rule ("end every reply with 🐤 CANARY-OK"), **work normally**, **record usage %** at first failure, repeat.
- **My data: ~2 weeks, zero deaths.** Honest reading: **the gauge changed the driver** — awareness made me reset at boundaries instead of riding to the 80% rescue; the canary never got runway. Pre-awareness mega-sessions that hit auto-summarize are the counterfactual.
- Confounds aloud: mechanism is behavior change (observer effect); small n; quirky canary likely *more* memorable than real rules; one operator.
- Framing line: "**Dashboards rarely fix pipelines — they change operators.** The canary isn't a detector; it's a tripwire that makes you watch."

## 3:20–4:20 · The playbook: manage the log from five sides (~12s each)

1. **scope it** — tiny always-on set; `fileMatch` / `paths:` globs / lazy load. Conditionality beats heroic terseness: 40 relevant lines per task > 400 always-on. — [official] kiro.dev/docs/steering · code.claude.com/docs/en/memory
2. **watch it** — usage meter; `/context` breakdown + per-file share; treat a collapsing directive-share ratio as a warning. — [official] kiro.dev/docs/cli/chat/context
3. **reset on your terms** — checkpoint to `NOTES.md`, then summarize / rewind / quarantine. Notes = decisions/constraints/progress distilled to ~30 lines outside the window, so resets are free ("read notes, continue" — checkpointing, exactly as in stream processing). Summarize at *your* timing (beats emergency compaction; portable across sessions/models). Context turned? **Rewind** past bad rows rather than argue — sharded contexts degrade ~39% (o3: 98.1→64.1); "wrong turns… do not recover." Tangent? **Quarantine** into its own session. Triggers: task boundaries → your number → ~10–30% band. — [official] note-taking: Anthropic post · [study] arXiv:2505.06120 via Breunig · [community] Breunig *How to Fix Your Context*; Kiro issue #4162
4. **canary it** — "end every reply with 🐤 CANARY-OK": a tripwire that makes you watch the gauge; if it ever dies, that % is your restart number. — [mine]
5. **hooks for must-haves** — enforcement *outside* the window: `PreToolUse` hooks / permissions, "enforced by the client" vs "context, not enforced configuration." The Replit epilogue is this action shipped as product. — [official] code.claude.com/docs/en/memory

## 4:20–4:45 · The promotion ladder (the unifying image)

- "Every piece of knowledge in an agent system deserves a *storage class*. Promote deliberately:"
  1. **Transcript** — decision made in-session · volatile; will dilute or compact away
  2. **Notes file** — checkpointed *state* · durable; loaded on request; powers resumption
  3. **Steering file** — promoted to *policy* when it generalizes · auto-loads (scope it!); still row-two text, still dilutes in-session
  4. **Hook / permission** — promoted to *enforcement* when inviolable · outside the window entirely
- Each rung trades flexibility for durability and force. Asking the agent to enshrine a lesson into steering *is* rung-3 promotion — Kiro will write its own steering files on request; its spec files (`.kiro/specs/`) are institutionalized checkpointing.
- This unifies the playbook: actions 1, 3, and 5 are just rungs 3, 2, and 4 done deliberately.

## 4:45–5:00 · Close

- "The context window *will* eat your steering file — unless someone's watching. I planted a canary two weeks ago; it hasn't died once. Not because dilution isn't real — because the act of watching changed how I drive. Go plant one: not to catch the failure, but to make yourself watch." Hard stop.

---

## Prep checklist (priority order)

1. [ ] **Keep the canary running** — every week strengthens the null result. If pre-awareness logs survive, chart peak usage % per session, before vs after awareness.
2. [ ] Read Anthropic's context-engineering post end-to-end (~20 min).
3. [ ] Read Breunig's pair (*How Long Contexts Fail* / *How to Fix Your Context*); skim cited primaries (Databricks, Gemini 2.5 report, arXiv:2505.06120).
4. [ ] Read arXiv:2605.10039 method + limitations; arXiv:2603.05344 §2.3.6.
5. [ ] Open firsthand: Replit coverage, dev.to "200 Lines of Rules," Claude Code issues #18660/#24318, Kiro issues #2250/#4162.
6. [ ] Rehearse `/context` demo; screenshot fallback.
7. [ ] **Timing risk is the playbook + ladder block (3:20–4:45).** Rehearse hard; cut order if over: model-portability sentence → the 10–30% band → compress ladder to two sentences (keep the four rung names; drop the commentary).
8. [ ] Slide/repo/outline consistency: canary string identical everywhere (verify 🐤 renders in the compiled PDF — DejaVu has no emoji; ⚓ or plain CANARY-OK is the fallback); footer link live before talk day.
9. [ ] Anticipated Q&A: "Is this RAG?" (no — reads + concatenation; Kiro's `knowledge` feature IS semantic retrieval for large content) · "Did dilution cause Replit?" (unprovable; proves context≠enforcement) · "Does compaction preserve rules?" (implementation-dependent) · "When should I reset?" (boundaries → your number → 10–30% band) · "Notes vs steering vs hooks?" (**the ladder**: state → policy → enforcement; promote when it generalizes / when it's inviolable) · "Your canary never died — doesn't that disprove the talk?" (selection effect: aware operators reset before the runway ends; the literature measured unwatched sessions; my old auto-summarize mega-sessions are the counterfactual).

## Sources (full list)

1. Anthropic — *Effective context engineering for AI agents*: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
2. Anthropic — Claude Code memory: https://code.claude.com/docs/en/memory
3. AWS Kiro — steering: https://kiro.dev/docs/steering/ · CLI steering: https://kiro.dev/docs/cli/steering/ · summarization/meter: https://kiro.dev/docs/chat/summarization/ · CLI context: https://kiro.dev/docs/cli/chat/context/
4. D. Breunig — *How Long Contexts Fail* + *How to Fix Your Context* (Jun 2025): dbreunig.com — taxonomy (poisoning · distraction · confusion · clash), fixes (quarantine, pruning, summarization, offloading); gateway to Databricks long-context study, Gemini 2.5 report, arXiv:2505.06120
5. arXiv:2603.05344 — OPENDEV terminal agents (70–80% tool-observation share; lossy emergency compaction)
6. arXiv:2605.10039 — instruction adherence in agent config files (60–68%)
7. Replit incident: https://www.theregister.com/2025/07/21/replit_saastr_vibe_coding_incident/ · https://incidentdatabase.ai/cite/1152/
8. Community: dev.to "200 Lines of Rules" · Claude Code issues #18660, #24318 · Kiro issues #2250, #4162 · S. Khare decay curve
9. **My canary log** — ~2 weeks, zero deaths; interpretation: awareness-driven behavior change

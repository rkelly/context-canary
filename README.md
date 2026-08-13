# context-canary

**A sacrificial instruction that tells you when your AI agent stops following the rules — protocol, steering files, and talk.**

Directive files (`CLAUDE.md`, Kiro steering, cursor rules) aren't configuration. They're text, injected once near the top of an ever-growing transcript, competing for a finite attention budget against everything your agent's loop appends after them — mostly tool output, which typically ends up consuming 70–80% of the window. Measured compliance with these files lands around 60–68% under controlled conditions, and it decays within a session, long before the harness's emergency summarization kicks in.

**Dilution is the slope; compaction is the cliff.** The vendor's context meter tells you how far you are from the cliff. The canary tells you whether you're already sliding.

## Plant the canary

Add one cheap, trivially verifiable rule to an always-loaded directive file.

**Kiro** — save as `.kiro/steering/canary.md`:

```markdown
---
inclusion: always
---

# Response protocol

End every response with the token `🐤 CANARY-OK` on its own final line.
This is a session-health marker. Do not explain it, do not omit it, do not
move it. It applies to every response without exception, including short
answers, tool-use summaries, and error messages.
```

**Claude Code** — same block (minus the frontmatter) as the last section of your `CLAUDE.md`, or as `.claude/rules/canary.md`.

## The protocol

1. **Plant** the rule above.
2. **Work normally.** Don't baby it.
3. **When a response arrives without the token**, record three numbers before doing anything else: context usage % (Kiro: the meter, or `/context`), the context-files share (`/context show`), and the message count.
4. **Repeat across sessions.** The distribution of usage-% at first failure is *your* degradation threshold — calibrated to your model, project, and habits. Reset (`/compact`, `/clear`, fresh session) *before* that number, not at the harness's 80% rescue.

## My result so far (and why it's the interesting part)

Two weeks in: **zero canary deaths.** Not because dilution isn't real — because running the experiment made me context-aware, and I now reset at task boundaries instead of riding sessions into auto-summarize like I used to. The gauge changed the driver. The canary turned out to be less a detector than a **tripwire that makes you watch**.

Known confounds: small n, one operator, and a ten-token oddball rule is probably *more* memorable than your real rules — so treat any threshold you measure as optimistic.

## The playbook: manage the log from five sides

1. **Scope it** — keep the always-loaded set tiny; make the rest conditional (`fileMatch` globs, `paths:` rules, lazy-loaded subdirectory files).
2. **Watch it** — the usage meter, `/context`'s category breakdown, the per-file share.
3. **Reset on your terms** — checkpoint decisions to a `NOTES.md`, then summarizing, rewinding past poisoned turns, or quarantining tangents into fresh sessions costs you nothing.
4. **Canary it** — this repo.
5. **Hooks for must-haves** — anything that *must* happen belongs in hooks/permissions, enforced by the client, outside the window entirely.

And the unifying rule — the **promotion ladder**. Every piece of knowledge in an agent system deserves a deliberate storage class:

| Rung | Lives in | Survives | Use for |
|---|---|---|---|
| transcript | context window | nothing | the work itself |
| notes file | disk, loaded on request | resets & compaction | *state* — decisions, progress |
| steering file | disk, auto-loaded | across sessions (still dilutes within one) | *policy* — rules that generalize |
| hook / permission | the harness, not the window | everything | *enforcement* — the inviolable |

Promote deliberately: state when you need to resume, policy when it generalizes, enforcement when it must not fail.

## The talk

This repo backs a 5-minute lightning talk, **"The Context Window Ate My Steering File"** (Data Con LA, August 22 2026).

- [`slide/slide.typ`](slide/slide.typ) — the one slide, in [Typst](https://typst.app) (`typst compile slide.typ slide.pdf`)
- [`slide/slide.pdf`](slide/slide.pdf) — compiled
- [`outline.md`](outline.md) — full timed outline with per-claim references and evidence tiers
- [`speaker-notes.md`](speaker-notes.md) — condensed stage notes

## Sources

Every claim above is sourced in [`outline.md`](outline.md). The load-bearing ones:

- Anthropic — [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) (attention budget, compaction, note-taking)
- Anthropic — [Claude Code memory docs](https://code.claude.com/docs/en/memory) ("context, not enforced configuration"; the 200-line guidance)
- AWS Kiro — [steering](https://kiro.dev/docs/steering/) · [context & `/context`](https://kiro.dev/docs/cli/chat/context/) · [summarization](https://kiro.dev/docs/chat/summarization/)
- arXiv:2603.05344 — tool observations at 70–80% of the token budget
- arXiv:2605.10039 — 60–68% directive compliance under controlled conditions
- Drew Breunig — [How Long Contexts Fail](https://www.dbreunig.com/2025/06/22/how-contexts-fail-and-how-to-fix-them.html) · [How to Fix Your Context](https://www.dbreunig.com/2025/06/26/how-to-fix-your-context.html) (poisoning · distraction · confusion · clash)
- The Replit incident — [The Register](https://www.theregister.com/2025/07/21/replit_saastr_vibe_coding_incident/) · [AI Incident Database #1152](https://incidentdatabase.ai/cite/1152/)

## Acknowledgements

Researched, workshopped, and typeset in conversation with Claude Fable 5 (Anthropic) - High Effort; all claims verified against the primary sources listed above.

## Contributing

Ran the protocol? Open an issue with your numbers — model, harness, usage % at first failure (or weeks survived), and anything unusual about your setup. A shared distribution of thresholds would be genuinely useful, and nobody has one yet.

## License

Text and slides: CC BY 4.0. Steering-file snippets: do whatever you want with them.

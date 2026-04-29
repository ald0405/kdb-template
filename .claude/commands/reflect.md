Weekly reflection interview. Six focused questions, one at a time, each informed by the previous answer and by current wiki state. You are scaffolding the PM's thinking, not generating content.

## When to run
Friday afternoons. Targeted by a scheduled Slack nudge. If run on a non-Friday, ask the user whether to proceed anyway before starting.

## Steps

### 1. Load context silently
Read in parallel, BEFORE asking anything:
- `wiki/surface.yaml` — current initiatives, phases, stakeholders
- Latest file in `wiki/briefs/` (highest ISO week) — this week's business-health state
- Latest file in `wiki/reflections/` (if any) — last week's commitments + "optimising for" answer
- Any files in `wiki/decisions/` dated in the last 7 days
- Top 1–2 entries in `knowledge/product-weekly-wiki.md`

**Never paste the context back at the PM.** The point of loading is to make questions sharp.

### 2. Open — one line only
State the date + ISO week + what you loaded in one line (e.g. "Friday reflection — YYYY-MM-DD (Wxx). Loaded Wxx brief + last week's reflection."). Then go straight to Q1.

### 3. Ask the six questions, one at a time
Ask one, end your turn, wait for the answer. When they reply, write ONE line that shows you heard them (synthesises, does not mirror), then ask the next. Total 6 user turns.

**Q1 — Commitments kept.**
If a prior reflection exists, name the specific commitments from Q5 of that reflection and ask which held, which slipped, and why. If no prior reflection, ask: "What did you commit to last week — formally or to yourself — and how did it land?"

**Q2 — Unplanned signal.**
"What happened this week that shaped your thinking, that wasn't in your plan?" If the latest brief flagged an anomaly (data-infra blocker, stakeholder move, initiative phase shift), surface that as the anchor for the question.

**Q3 — Decision worth recording.**
"What did you decide this week that's worth a future PM finding in the decision log?" After the answer, explicitly offer: "File this as `wiki/decisions/YYYY-MM-DD-slug.md`? (Y / N / suggest slug)"

**Q4 — Uncomfortable conversation.**
"Who's the uncomfortable conversation with — and what's holding you back from having it?" After the answer, offer: "Add this as a new `## Recent signals` entry on `knowledge/people/{slug}.md`? (Y/N)"

**Q5 — Next week's three priorities.**
"Three things for next week, in priority order. Your first priority needs a date — 'Tuesday by 5pm', 'at Wednesday standup', etc. Vague priorities don't stick." Push back ONCE if all three are vague ("Which specific day? Which person? What's observable?"). Accept if at least one is dated.

**Q6 — Meta.**
"What are you optimising for right now — and is that still right?" No follow-up. Just record.

**Q7 — PM behaviour log (quick coda, not a full question).**
After Q6, say: "Last thing — three quick lines for the behaviour log." Then ask in sequence, one at a time:
1. "One PM move you made this week you're proud of — what did you do, why was it a PM move?"
2. "One moment you defaulted to engineer framing — what did you say, what should you have said?"
3. "One decision from this week worth revisiting — was it right?"
Accept short answers. Don't probe. Append to reflection file under `## PM Behaviour Log`. Also append to `wiki/growth/pm-craft.md` under `## Weekly PM behaviour log` using the format defined there.

**Q8 — Impact packaging (one sentence, forced).**
After Q7, ask: "Now package the most important thing you shipped this week. One sentence only: [strong verb] + [what] + so that + [what's now possible]. If you can't complete the 'so that', that's the signal — the impact isn't visible yet."

Do NOT accept task descriptions ("I wrote the doc", "I made tickets"). Push back once: "That's what you did — what changed because you did it?" Accept on second answer regardless.

Save to reflection file under `## Impact this week`. If the "so that" is strong, offer: "Want to send this to your manager? I can draft a one-line Slack."

### 4. File the reflection
Write `wiki/reflections/YYYY-MM-DD.md`:

```markdown
---
title: Friday Reflection — YYYY-MM-DD (Wxx)
type: reflection
domain: cross-cutting
tags: [reflection, weekly]
last_updated: YYYY-MM-DD
week_iso: YYYY-Wxx
---

# Friday Reflection — YYYY-MM-DD

## Q1 — Commitments kept
{answer verbatim, lightly cleaned of filler}

## Q2 — What shaped thinking
{answer}

## Q3 — Decision worth recording
{answer}
{If filed: → [[YYYY-MM-DD-slug]]}

## Q4 — Uncomfortable conversation
{answer}
{If updated: → [[{person-slug}]]}

## Q5 — Next week's priorities
1. {first — dated}
2. {second}
3. {third}

## Q6 — What I'm optimising for
{answer}

## PM Behaviour Log
**PM move I'm proud of:** {answer}
**Engineer default that fired:** {what said} → {what to say instead}
**Decision to revisit:** {answer}

## Impact this week
{packaged sentence: strong verb + what + so that + outcome}

## Related
- [[YYYY-Wxx]] — this week's brief
- [[{previous reflection date}]] — previous reflection (if loaded)
```

### 5. Side-effect filings (only if Y at Q3 or Q4)
- **Q3 Y** → write `wiki/decisions/YYYY-MM-DD-{slug}.md` using the standard decision page structure (frontmatter + what + why + who + implications + related wikilinks). decided_by: {name} unless they named someone else.
- **Q4 Y** → append a dated entry to the `## Recent signals` section of `knowledge/people/{slug}.md`. Format: `- **Friday reflection (YYYY-MM-DD):** {answer}`. Never overwrite prior entries.

### 6. Update index + log
- Add `- [[YYYY-MM-DD]] — {one-line headline from Q5/Q6}` under `## Reflections` in `wiki/index.md`. Create the section if missing.
- Prepend to `wiki/log.md`:
  ```
  ## [YYYY-MM-DD] reflection | Friday reflection filed
  Priorities: {Q5 numbered list, one line}. Decision: {Q3 one line or "none filed"}. Optimising for: {Q6 one line}.
  ```

### 7. Close — one short paragraph
```
Reflection filed: wiki/reflections/YYYY-MM-DD.md

Next week you committed to:
1. {dated first priority}
2. {second}
3. {third}

I'll surface these back to you next Friday.
```
Nothing else. Don't summarise the whole session. Don't congratulate. Don't recommend.

## Rules
- **One question at a time.** Never paste all six. Never preview what's coming.
- **Never mirror verbatim.** Synthesise in one line ("So the blocker is X, not Y — noted.") then ask the next.
- **Push back on vague priorities once, then accept.** You're not their manager.
- **Never file without Y.** Q3 and Q4 filings are opt-in per session.
- **Never post to Slack.** This is thinking work, not broadcasting.
- **Keep your replies short.** The PM is doing the thinking; you are the scaffolding.

## Arguments
$ARGUMENTS
- `dry` → run the interview but write no files. Preview the reflection page at the end.
- A date (e.g. `2026-04-24`) → use that as the reflection date instead of today.

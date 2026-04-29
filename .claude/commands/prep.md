Generate a pre-meeting prep brief for a specific person or meeting.

## Usage

`/prep [person]` — e.g. `/prep VP Product` or `/prep CTO`
`/prep [meeting type] with [person]` — e.g. `/prep roadmap review with Director of Engineering`

If no arguments provided, ask who the meeting is with and what type of meeting it is.

## Steps

### Step 1 — Identify the person

Match the argument to a person in `knowledge/people/`. Use fuzzy matching — "VP" matches "VP Product", role title or first name matches the slug.

If no match: list who's in `knowledge/people/CLAUDE.md` and ask to clarify.

### Step 2 — Read their profile

Read `knowledge/people/{slug}.md`.

If the file is still a stub (Last updated says "not yet scanned"):
- Note this and tell the user to run `/stakeholder-scan` first, OR
- Ask if they want to fill in what they know manually right now

### Step 3 — Load context

Read `knowledge/okrs-and-sprint-goals.md` — current priorities and sprint goals.
Read `knowledge/team.md` — team context and sign-off matrix.

### Step 4 — Generate the prep brief

Output a focused prep brief using this structure:

```
*PREP — [Meeting type] with [Name]*

*WHO THEY ARE*
[Their role and the lens they bring — one line]

*WHAT THEY CARE ABOUT*
[3 bullets from their profile — tailored to the meeting context if known]

*QUESTIONS THEY'RE LIKELY TO ASK*
[3-4 questions based on their observed patterns — specific to this meeting topic if possible]

*WHAT TO LEAD WITH*
[What framing will land well given their decision style]

*WHAT TO BE READY TO DEFEND*
[Where they typically push back — gaps they tend to flag]

*RECENT SIGNALS*
[Anything notable from their last scan that's relevant to this meeting]
```

Keep it under 20 lines. This is a prompt, not a briefing document.

### Step 5 — Offer follow-up actions

After the brief, offer:
- "Want me to draft talking points for any agenda items?"
- "Want me to check the OKRs for anything specifically relevant to this meeting?"
- "When did this profile last update? Run /stakeholder-scan to refresh."

# Sample relay (research + draft task)

This is an example baton, not instructions for the current user.

````markdown
## BROKEN TELEPHONE (protocol v1)
pass: 1
from: assistant-in-long-chat
to: next AI in a fresh chat
updated_at: 2026-09-28T01:00:00Z

### Paste this as the first message in the new chat

You are continuing an unfinished task. A previous assistant hit context limits and handed you this baton. Do not restart from scratch. Do not re-ask for information that is already in this packet. Read the whole packet, confirm goal + next action in one short paragraph, then do the next action.

### Goal
Write a 6-section briefing on why municipal libraries lose teen visitors, with sources and a one-page rec set for a library board.

### Current state
Outline and sections 1–3 are drafted in `briefing-draft.md`. Section 3 still needs two citations. Sections 4–6 and the board rec page are not started. User wants plain language, no academic tone.

### Done (do not redo)
- Confirmed scope: teens 13–18, public libraries in mid-size US cities, last 10 years.
- Rejected "just add more screens" as the main recommendation (user said that).
- Drafted sections 1–3 in `briefing-draft.md`.
- Collected 8 sources in `sources.md` (3 still unused).

### Next action (do this first)
Open `briefing-draft.md` and `sources.md`. Add two citations to section 3 from the unused sources. Do not rewrite sections 1–3.

### After that
1. Draft section 4 (what libraries tried that failed) from remaining sources.
2. Draft section 5 (what actually moved teen visits).
3. Draft section 6 + one-page board rec.
4. Pass the full draft back for user edits.

### Constraints (keep)
- Plain language. No "landscape" / "tapestry" filler.
- Every claim in sections 4–6 needs a source already in `sources.md` or a newly found primary source.
- Board rec page must fit on one printed page.
- Do not recommend blocking phones.

### Decisions already made
- Audience is the library board, not researchers.
- Structure is 6 sections + rec page (user approved).

### Open questions
- Budget cap for any recs? (UNRESOLVED — do not invent a number)

### Important files / artifacts
- `briefing-draft.md` — live draft
- `sources.md` — bibliography and quotes

### Known issues
- One blog source in `sources.md` is weak; do not build a section on it.

### Skills / tools the next AI should use
- Deep research only if `sources.md` cannot cover sections 4–6.
- Doc skill if the user asks for a .docx at the end.

### What not to do
- Do not restart the outline.
- Do not rewrite sections 1–3.
- Do not add a seventh section.

### First user-visible reply
Confirm you loaded the baton. State you will cite section 3 next. Then do that edit.
````

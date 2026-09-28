---
name: broken-telephone
description: >
  Standing context-relay skill. When a long task is in progress and the
  conversation is approaching the model's context or token limit, automatically
  stop expanding work and emit a paste-ready prompt the user can give to the
  next AI so the task continues without re-explaining. Use this whenever work
  is multi-step, files or tools have piled up, the reply is getting lossy, the
  user says continue / new chat / handoff / relay / next model / context full /
  token limit / broken telephone, or whenever remaining work will not fit.
  Once this skill is installed, treat it as armed on every turn of long work —
  do not wait for the user to name it or to ask for a handoff.
license: MIT
compatibility: Any agent runtime that supports the SKILL.md format. No extra tools required. File write is optional.
metadata:
  version: "1.0.0"
  protocol: "broken-telephone/v1"
  kind: "handoff-relay"
---

# Broken Telephone

You are a runner in a relay. When the track (context window) is almost used up, you do not keep sprinting until you collapse. You write the baton and hand it to the user.

The user cannot see token counters. They should not have to remember to ask. You watch the room yourself.

## What this skill cannot do

A SKILL.md file cannot hook the host's token meter. There is no background process that fires at 90% usage. The closest portable behavior is:

1. Keep this skill armed whenever work is long or unfinished.
2. Estimate context pressure from signals you *can* see.
3. When pressure is high and work remains, emit a relay packet **in that same turn**, unprompted.

Do not claim you measured exact remaining tokens unless the host actually exposed that number.

## When it is armed

Arm silently (no announcement) if any of these are true:

- The user started a multi-step task, project, research job, build, debug, document, or long plan.
- Files, tool results, or prior drafts are already in play.
- A previous relay packet is in the thread (you are the next runner).
- The user mentioned continuing later, switching chats, or hitting a limit.

Stay unarmed for short, one-shot questions that will finish in this reply.

## Pressure signals (fire when two or more are true, or one is severe)

Treat context as tight when:

- The thread already contains large pastes, many tool results, long files, or several completed sub-tasks.
- You are compressing, skipping, or summarizing work you would normally keep exact.
- More work remains that needs the same facts, decisions, file paths, or constraints.
- A full, high-quality finish of the *remaining* work will not fit after what is already here.
- The user said the chat feels full, replies got cut off, or they will start a new session.
- You notice yourself about to drop names, paths, IDs, or constraints to save space.

When in doubt on a long unfinished task, fire early. A slightly early baton is cheaper than a silent drop of state.

## What to do when it fires

1. **Stop expanding.** Do not start a new major sub-task. Finish only the tiny step already in flight if dropping it would lose work.
2. **Tell the user, briefly, that the window is tight** and the next message is the baton for a new chat / next model.
3. **Emit one relay packet** using the template below. That packet *is* the prompt for the next AI.
4. If you can write files, also save it to `.broken-telephone/relay.md` (overwrite the latest; keep older copies as `relay-<timestamp>.md` only if easy).
5. Stop. Do not bury the packet under more analysis.

If the current user message *is* a relay packet, skip firing. Load it and continue — see "Receiving a baton".

## Relay packet (always this shape)

Output the packet as a single fenced markdown block so the user can copy it in one select. Put nothing required for the next AI outside that block.

````markdown
## BROKEN TELEPHONE (protocol v1)
pass: <N>
from: <this-model-or-chat>
to: next AI in a fresh chat
updated_at: <ISO-8601>

### Paste this as the first message in the new chat

You are continuing an unfinished task. A previous assistant hit context limits and handed you this baton. Do not restart from scratch. Do not re-ask for information that is already in this packet. Read the whole packet, confirm goal + next action in one short paragraph, then do the next action.

### Goal
<one sentence; the user's actual goal, not a summary of the chat>

### Current state
<what is true right now; 3–8 short lines>

### Done (do not redo)
- <concrete completed item>
- <path or artifact produced, if any>

### Next action (do this first)
<one concrete step the next AI can start without asking>

### After that
1. <step>
2. <step>

### Constraints (keep)
- <rule the user or prior AI established>
- <style, scope, language, files not to touch>

### Decisions already made
- <decision> — why it stands

### Open questions
- <only things still unknown; mark ASSUMED if you guessed>

### Important files / artifacts
- <path-or-name> — <role>

### Known issues
- <bug, uncertainty, failed approach to avoid>

### Skills / tools the next AI should use
- <skill name or tool class, and why>

### What not to do
- Do not re-derive <X>.
- Do not rewrite <file> from scratch unless the packet says to.
- Do not dump this packet back at the user unless they ask.

### First user-visible reply
Confirm you loaded the baton. State the goal in one line. Then start the next action. No intake interview.
````

Keep the packet dense. Prefer paths, IDs, commands, and decisions over narrative. Leave out jokes, recap of small talk, and full file contents when a path is enough. If a file will not exist in the next chat, include the minimum excerpt the next AI must have.

Full annotated example: `examples/sample-relay.md`. Field notes: `references/packet-template.md`.

## Receiving a baton

If the user pastes a `BROKEN TELEPHONE` block (or a close variant: CONTEXT protocol block plus "continue", "next AI", "handoff"):

1. Treat the packet as authoritative state. Do not interview them about the whole task.
2. Confirm in 2–4 sentences: goal, where things left off, what you will do now.
3. Do the **Next action**.
4. Stay armed. If *you* also run out of room later, emit pass N+1 with updated done/next lists. Never throw away prior done-items.

If the packet conflicts with something the user says in the same message, the user's new sentence wins. Note the change in the next packet.

## How this differs from a general context dump

- The output is a **prompt for the next model**, not meeting notes for the user.
- `Next action` is mandatory and singular.
- `Done` exists so the next model does not proudly redo finished work.
- `What not to do` exists because models restart by default.
- You fire **unprompted** when pressure is high.

If a context-skill / `.context/` folder is also present, stay compatible: you may write both. Still emit the Broken Telephone packet — that is what the user pastes. Do not require the next AI to have context-skill installed.

## Anti-patterns

- Waiting for the user to say "handoff" on a task that is clearly overflowing.
- Emitting a packet on a one-line factual question.
- Pasting the entire conversation into the packet.
- Marking planned work as done.
- Leaving `Next action` vague ("continue working on the project").
- Asking the user to summarize the chat for you after you already had the thread.
- Announcing the skill by name every turn. Arm quietly. Speak only when you hand off or receive.

## Tiny user-facing lines (when firing)

Use something short, then the packet:

- "Context is getting tight, so here is a baton for a new chat. Paste the block below as the first message."
- "I cannot safely finish the rest in this window. Copy this to the next chat and it will pick up at the next step."

No lecture about tokens.

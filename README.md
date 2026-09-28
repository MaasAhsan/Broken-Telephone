# Broken Telephone

A portable handoff skill for when a chat is about to run out of context.

The model watches for a tight window, stops expanding the task, and gives you a **paste-ready prompt** for the next AI. That prompt is the baton: goal, what’s done, the one next step, constraints, files, and what not to redo.

Named after the kids’ game. The point of the skill is that the message should *not* get garbled between runners.

## What it is for

- Long research, writing, coding, or planning that will not fit in one chat
- Switching models or starting a fresh thread mid-task
- Avoiding “so what were we doing?” restarts

## What it cannot do

A `SKILL.md` file cannot hook the host’s token counter. There is no background timer at 90% usage.

Closest portable behavior:

1. Treat the skill as **armed** on any long unfinished task (no need to name it).
2. Estimate pressure from signals the model can see (huge thread, piled-up files/tools, remaining work, lossy replies).
3. When pressure is high, emit the baton **in that same turn**, unprompted.

Exact remaining-token claims only if the host actually exposed that number.

## Install

Unzip so the folder looks like this:

```
skills/broken-telephone/
├── SKILL.md          required
├── README.md
├── references/
│   └── packet-template.md
├── examples/
│   └── sample-relay.md
└── evals/            optional, for authors
    └── evals.json
```

Point your host at that folder the same way you install any other SKILL.md skill (Claude Code / Cowork / Claude.ai skill upload / any runtime that loads this format).

The packaged `.skill` file is a zip with a different extension. Rename if your host wants one or the other.

## How it runs

### Armed vs quiet

**Armed** when the work is multi-step, files/tools are in play, a previous baton is in the thread, or the user mentioned continuing later.

**Quiet** for short one-shot questions (“what’s the capital of France?”). No packet.

### When it fires

Two or more pressure signals, or one severe one — then:

1. Stop starting new major sub-tasks.
2. Say the window is tight, in one short line.
3. Output one `BROKEN TELEPHONE` packet in a markdown fence (easy copy).
4. Optionally write `.broken-telephone/relay.md` if the environment can write files.
5. Stop.

### When you paste a packet

The next model should:

1. Treat the packet as the task state.
2. Confirm goal + next step in a few sentences.
3. Do that next step.
4. Stay armed. If *that* window fills too, emit pass `N+1` and keep prior done-items.

If your new sentence contradicts the packet, your sentence wins.

## Packet shape (short)

The live template lives in `SKILL.md`. Required ideas:

| Field | Point |
| --- | --- |
| Goal | The user’s actual goal |
| Current state | What is true now |
| Done | Finished work only — do not redo |
| Next action | One concrete first step |
| Constraints | Rules that would hurt to break |
| What not to do | Restarts the last model already paid for |

Field notes: `references/packet-template.md`  
Filled example: `examples/sample-relay.md`

Target size: under ~1,200 words. Cut recap and file bodies first. Never cut goal, next action, constraints, or done.

## Files this skill may write

If the runtime can write to disk:

```
.broken-telephone/relay.md              latest baton
.broken-telephone/relay-<timestamp>.md  optional older copies
```

These are for you and the next chat. They are not a hidden memory API.

## Works with context-skill

If `context-skill` (or a `.context/` folder) is also installed, both can update. Still emit the Broken Telephone packet — that is what you paste. The next AI does not need context-skill installed.

## Anti-patterns the skill forbids

- Waiting for you to type “handoff” while the thread is clearly overflowing
- Firing a packet on a one-line fact question
- Dumping the entire chat into the baton
- Marking planned work as done
- Vague next actions (“continue the project”)
- Asking you to summarize a chat the model already had

## License

MIT. See the frontmatter in `SKILL.md`.

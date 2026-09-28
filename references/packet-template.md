# Packet field notes

Load this only if the shape of the baton is unclear.

## Required fields

| Field | Rule |
| --- | --- |
| `pass` | Integer. First handoff is `1`. Increment on each later handoff. |
| `goal` | The user's goal, not "help the user with their request". |
| `current_state` | Present tense. What is true now. |
| `done` | Only finished work. Artifacts get paths. |
| `next action` | One step. A verb plus an object. Runnable without a question. |
| `constraints` | Anything that would be expensive to violate. |
| `what not to do` | Restarts and rabbit holes the previous model already paid for. |

## Optional but useful

- `decisions` — stops the next model from re-opening closed calls.
- `open questions` — only real unknowns. Tag guesses `ASSUMED`.
- `important files` — path + role. Quote a slice only if the file will not exist next chat.
- `skills / tools` — names the next model should look for.
- `first user-visible reply` — keeps the next model from doing an intake interview.

## Size budget

Aim for a packet the user can paste without hitting *their* paste limit. Rough target: under ~1,200 words. Cut in this order: chat recap, then tool logs, then file bodies (keep paths), then secondary plans. Never cut goal, next action, constraints, or done.

## Provenance

If you inferred a constraint, say so in the line. Do not present a guess as a user rule.

## Pass chaining

Pass 2+ packets must carry forward every still-true done item and constraint. Summarize old done-items; do not drop them. Add what this pass finished to `done`.

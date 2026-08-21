# Naming conventions

Rules for naming issue folders and the files inside them. `SKILL.md` covers
*when* to create things; this covers *what to call them*.

## Issue folder names (`.context/<ISSUE-ID>/`)

**Ticket-backed issues** — use the external tracker's ID verbatim. Don't
reformat, lowercase, or abbreviate it.

```
.context/FW-1427/      # matches the tracker exactly
.context/fw-1427/      # don't lowercase it
.context/FW1427/       # don't "fix" its formatting
```

Preserving the ID exactly matters more than it looks: it's what lets the
folder name double as a lookup key back into the tracker, and what keeps a
single canonical spelling in use across every issue instead of several.

**Self-directed issues** - a naming you choose. Kebab-case, lowercase,
describing the *feature or system*, not the task:

```
.context/payment-system/           # names the system
.context/nvme-enumeration/         # names the feature/bug
.context/fix-payment-bug-2/        # names a task, and won't age well
.context/refactor/                 # too generic, will collide eventually
```

A useful test: if you'd still recognize what this issue is about by folder
name alone in six months, it's specific enough.

- Avoid slugs matching the `LETTERS-NUMBER` pattern (`payment-4`, `auth-2`).
- Once created, don't rename an issue folder after work has started.
- If the ID genuinely has to change (e.g. a ticket gets renumbered by the 
  tracker), rename the folder and add a line to the new `ISSUE.md`:
  ```markdown
  - redirected-from: FW-1427
  ```

## File names inside an issue folder

**The six core files are reserved names, exact casing, no exceptions:**

```
ISSUE.md  FINDINGS.md  ATTEMPTS.md  CONVERSATIONS.md  CONNECTIONS.md  HANDOFF.md
```

Don't repurpose one of these names for something else, and don't create a
near-miss (`Findings.md`, `finding.md`).

**Free-form notes** - lowercase-kebab-case `.md` files, named for their
actual content, not generically:

```
what-we-tried-before-reopening.md   # says why it exists
vendor-datasheet-notes.md           # says why it exists
notes.md                            # tells you nothing
misc.md                             # defeats the point of a structured system
```

**Non-markdown attachments** (logs, traces, screenshots, register dumps): If an 
issue accumulates more than a handful of these, consider nesting them under an 
`attachments/` subfolder inside the issue folder so the top level stays scannable - 
the six core files plus a couple of note files at a glance, not a directory listing 
full of log dumps.

## Cross-referencing another issue

When referencing another issue in prose, like inside `CONVERSATIONS.md`,
`ISSUE.md`, or a free-form note, always use its literal folder name, not
a paraphrase, so references stay greppable.

Structured issue-to-issue relationships still belong in `CONNECTIONS.md`
- this rule is about inline mentions elsewhere, not a substitute for it.

---
name: context-manager
description: Use this skill when working on a complex engineering task that spans multiple sessions or multiple coding agents (Claude Code, Codex, etc.) - creating, reading, and updating a persistent .context/ISSUE-ID/ folder that tracks current progress on the task, prior conversations, attempted approaches, and confirmed findings so no context is lost between sessions. Trigger when starting or continuing work on a feature or a system (e.g. "payment system", "user auth", "subscription model"), a specific ticket with an ID (e.g. JIRA-123, FW-1427), resuming a previous investigation, when the user references "the ticket", "what we tried before", "where we left off", "worked in a different session", "continue working on", "based on the findings", or similar, asks to log a finding or an attempt.
---

# Context Manager

## About

This skill is aimed to help developers when working on big features, long-lasting 
tickets or anything else which is being worked on for a long period of time and 
needs constant context update and tracking to keep everything in mind - these are 
the areas where this skill works the best at.

## Issues

> An "issue" in this document always means something with its own `.context/`
> folder - not an arbitrary request or sub-step within a session. 

An **issue** is anything tracked in this system: a feature, a bug, an investigation,
a ticket, anything substantial enough to span multiple sessions and need its own
`.context/<ISSUE-ID>/` folder.

Issues should be self-contained, should provide information only related to the given 
issue, shouldn't span across multiple issues, even if they are closely related, should
have a main problem statement or a concrete feature request and have a clear goal which
has to be achieved.

This means that when working on an issue, the context for that given issue should be 
isolated and scoped only to issue-related information, and not to the general feature 
or system being worked on. This is the core principle of this skill - to retain 
information only relevant to the issue being worked on to keep the context clear and concise.

In cases of interconnected issues, the context for each issue should be kept in its own 
`.context/<ISSUE-ID>/` folder, and any cross-references should be made explicit in the 
relevant files (e.g., in ISSUE.md or CONNECTIONS.md).

Every issue is usually of two kinds:
- **Ticket-backed** - comes from an external tracker (Jira, GitHub Issues, etc.)
  and already has an ID, e.g. `FW-1427`. The folder name is that ID, verbatim.
  If user mentions a ticket ID, the agent should always use it as the main 
  identifier for the issue, and in all other references to the issue, prefer that ID. 
- **Self-directed** - you're splitting the project into a separate issue for 
  the main goal and your own tracking purposes, e.g. `payment-system`, 
  `nvme-enumeration`, etc. The folder name is a slug you choose.

## The .context/ folder

Each issue gets its own subfolder, named after its ticket ID, at the root of the repository (or wherever the project's context root is configured):

```
.context/
└── <ISSUE-ID>/              # e.g. .context/FW-1427/ or .context/something-support/
    ├── ISSUE.md             # what the issue is: description, scope, acceptance criteria, links
    ├── FINDINGS.md          # durable technical facts discovered along the way
    ├── ATTEMPTS.md          # every approach tried, and its outcome
    ├── CONVERSATIONS.md     # context and decisions from humans (comments, calls, chat)
    ├── CONNECTIONS.md       # relationships and dependencies between issues
    ├── HANDOFF.md           # notes for the next session or agent
    ├── *.md                 # free-form notes as needed (e.g. some-note.md)
    └── *                    # any other files relevant to the issue (logs, screenshots, diagrams, etc.)
```

The exact structure expected inside each file is in `references/file-schemas.md`. Folder and file naming rules are in `references/naming-conventions.md`.

## Workflow

### 1. Starting work with a new issue

Check whether .context/<ISSUE-ID>/ already exists.

- It doesn't exist - this is a fresh start. Create the folder and scaffold the standard files. If 
`scripts/new_issue.sh` is present - use it, otherwise copy the templates from assets/templates/. 
Populate `ISSUE.md` from whatever the user or issue description provides - don't leave it blank.

- It already exists - this is a resume, not a fresh start. Go to step 2 before writing or changing anything.

### 2. Resuming an issue

Before taking any action, read the existing context in this order: `ISSUE.md`, `FINDINGS.md`, `ATTEMPTS.md` and
then `HANDOFF.md` (if it exists). These are the core foundational files for the issue, the rest of the files are not as important
when resuming an issue from a fresh session and can be read as needed.

Summarize back to the user what's already known and what was last being tried, so the user and the agent start from 
the same state. Don't silently re-derive context the folder already has, and don't re-attempt something `ATTEMPTS.md` 
already ruled out without saying so.

### 3. While working

- **Tried an approach?** - append an entry to `ATTEMPTS.md`: what was tried, why, and the outcome - whether it 
  worked or not. A failed attempt is exactly as valuable as a successful one; the entire point is never 
  repeating a dead end.

- **Learned something durable about the system?** (a root cause, a hardware constraint, how a subsystem actually 
  behaves) - append it to `FINDINGS.md`. This file is signal, not narrative: short, factual entries, not a transcript.

- **Got input from a human?** (a decision, a clarification, a ticket comment) - append it to `CONVERSATIONS.md`, 
  attributed to its source. 

- **Found a dependency on, or relationship to, another issue?** - append to `CONNECTIONS.md`, referencing the 
  other issue's ID directly and how they connect.
  
Never delete or rewrite history in these files - append and date-stamp instead. The value of `.context/` is the trail,
not just the current state.

### 4. Ending a session or handing off

Sessions have to be ended explicitly by the user, don't assume that a session is over by youself. User has to explicitly 
say that the session is over, or that they are handing off to another agent.

Before ending a session, write a short note about the current progress and status: current progress, what was last tried, 
what is next to try, and any other relevant context. Put this information in `HANDOFF.md`, overwriting the previous contents -
treat this file as the handoff mechanism between sessions and between agents. Chat history will not be there next time; 
the `.context/` folder is the only thing that reliably is.

## Guidelines

- One `.context/<ISSUE-ID>/` folder per issue
- Never share a folder across issues, even closely related ones
- Don't duplicate the same fact across files: `FINDINGS.md` is the single source of truth for technical facts, `ATTEMPTS.md` for 
  what was tried, `CONVERSATIONS.md` for what was received from human input or additional external conversations. If you're not 
  sure which file something belongs in, see `references/file-schemas.md`
- If the project has no `.gitignore` entry for `.context/`, ask the user whether context should be version-controlled before 
  assuming - some teams want it committed and shared, others want it local-only
- `FINDINGS.md`, `ATTEMPTS.md` and `CONVERSATIONS.md` are append-only logs, never edit or delete existing entries, 
  only add.
- `ISSUE.md`, `HANDOFF.md` and `CONNECTIONS.md` are living documents - edit them in place to reflect current state.

## See also

- `references/file-schemas.md` - exact structure and required sections of each file
- `references/naming-conventions.md` - folder and file naming rules
- `references/session-handoff.md` - multi-agent handoff protocol, in detail
- `assets/templates/` - blank templates used when scaffolding a new issue
- `examples/FW-1427/` - a fully worked, real-world BIOS issue end to end

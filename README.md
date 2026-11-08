# Context Manager

An agent skill for preserving engineering context across long-running tasks, sessions, and coding agents.

## About

Some engineering tasks don't fit in one session. A single feature implementation or ticket can span a large, unfamiliar codebase, behavior tied to specific hardware or platforms, and context pulled from the task's description, chat threads, documentation, and prior debugging - accumulated over days or weeks, often across more than one coding agent.

In those situations conversation history doesn't survive across sessions or across agents. This skill keeps that context in the filesystem instead, so any agent picking up the progress can reconstruct the full picture before doing anything.

This skill is aimed to help developers when working on big features, long-lasting tickets or anything else which is being worked on for a long period of time and needs constant context update and tracking to keep everything in mind - these are the areas where this skill works the best at.

It tracks:

- the issue, scope, and acceptance criteria
- confirmed technical findings
- attempted approaches and their outcomes
- durable decisions, their rationale, and resulting impact
- relationships between issues
- the current handoff state and next action

> [!TIP]
> It is very recommended to use this skill in combination with the [caveman skill](https://github.com/JuliusBrussee/caveman) to make the agent output more concise and compressed, saving many tokens in the process of writing and reading of the context files

## How it works

Each issue gets a self-contained folder:

```text
.context/
├── ISSUES.md                 # project index of managed issues
└── <ISSUE-ID>/
    ├── ISSUE.md
    ├── FINDINGS.md
    ├── ATTEMPTS.md
    ├── DECISIONS.md
    ├── CONNECTIONS.md
    ├── HANDOFF.md
    ├── notes/
    ├── sources/
    └── attachments/
```

To scaffold an issue manually, run the bundled helper from the installed skill directory:

```bash
scripts/new_issue.sh <issue_id> --kind <issue_kind> --title <issue_title> --description <short_description>
```

## Install

### Claude Code

> Recomended for Claude Code in specific

Add my custom skills marketplace:

```bash
claude plugin marketplace add duckysmacky/duckysmacky-plugin-marketplace
```

Install the skill as a Claude Code plugin:

```bash
claude plugin install context-manager@duckysmacky
```

### Any other agents

`npx skills` supports Claude Code, Codex, Cursor, OpenCode, Gemini CLI, GitHub Copilot, and other agents. Let it detect installed agents automatically:

```bash
npx skills add duckysmacky/context-manager-skill
```

## Example

For example, a user can start that issue by invoking the skill with its ticket and
initial context:

```
/context-manager Let's work on Jira ticket 'FW-1427'. Here is its description: "cascaded NVMe shelves on a dual-socket server fail to expose all 96 drives. ....". Also here is something that I learned after talking with the engineers: "...". Search the codebase for hot paths, possible issues and related code, then return with a report on your findings
```

The skill recognizes `FW-1427` as a new ticket-backed issue, scaffolds
`.context/FW-1427/`, and records the supplied problem statement in `ISSUE.md`.
The completed example shows how that context can evolve during the work.

Also, the repository and skill include [a complete real-life example](skills/context-manager/examples/FW-1427/) of the skill's workflow being used.

The materials were taken from a **real BIOS project**, where a single Jira ticket was worked on for ~3 months, with complex research, many attempts, and incremental solution discovery. It contains technical investigation, chronology, failed attempts, findings, conversations, and handoff state, providing a practical showcase of how the skill can be used throughout a complex real-world project.

> [Note]
> All sensitive and private information from the source folder was removed or anonymized to comply with company policies. In those places, the example provides renamed terms and placeholder files (such as logs), but all technical depth and the core issue were preserved.

## Motivation

I used to work at a company as a BIOS developer and worked on some really complex tasks, which weren't usually solved fast, taking days, weeks or even months to complete. This is because each task included working with a huge BIOS codebase, doing a lot of hands-on work with the platform's hardware, trying to find where the related code is located within the repository, and dealing with lots of information and context, collected from different sources like Jira ticket descriptions, personal conversations, documentation and so on. And each single task was usually completed utilizing multiple sessions and coding agents.

Basically, that was a lot of information to keep track of! For both humans and agents, where the latter in specific tend to run out of context windows, start wasting too many tokens, get lost or have to re-remember everything between sessions.

So I decided to create a `.context` folder, where each inner subfolder was its own Jira ticket being worked on. Inside that folder I placed each relevant piece of information which was used to successfully close the given Jira ticket. This included the Jira ticket's description, extracted information from personal conversations with engineers about the core issue, what was already tried, different findings, and so on.

At the time that I was working, I guided my agents to this folder using global and local AGENTS.md and CLAUDE.md, but now I decided to go back to this system and turn it into a proper skill. Basically, it structures the process of working with this `.context` folder, describes how files should be named, what to place in each file, and how to navigate and work with context files, transforming my previous AGENTS.md-contained system into a production-ready skill which can be used by engineers working on big and complex tasks requiring lots of external information.

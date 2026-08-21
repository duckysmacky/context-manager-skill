# Context Manager Skill

An agent skill for managing big amounts of context for complex and long-running development tasks

## About

Some engineering tasks don't fit in one session. A single feature implementation or ticket can span a large, unfamiliar codebase, behavior tied to specific hardware or platforms, and context pulled from the task's description, chat threads, documentation, and prior debugging - accumulated over days or weeks, often across more than one coding agent. 

In those sutiations conversation history doesn't survive across sessions or across agents. This skill keeps that context in the filesystem instead, so any agent picking up the progress can reconstruct the full picture before doing anything.

## Motivation

I used to work at a company as a BIOS developer and worked on some really complex tasks, which weren't usually solved fast, taking days, weeks or even months to complete. This is because each task included working with a huge BIOS codebase, doing a lot of hands-on work with the platform's hardware, trying to find where the related code is located within the repository, and dealing with lots of information and context, collected from different sources like Jira ticket descriptions, personal conversations, documentation and so on. And each single task was usually completed utilizing multiple sessions and coding agents.

Basically, that was a lot of information to keep track of! For both humans and agents, where the latter in specific tend to run out of context windows, start wasting too many tokings, get lost or have to re-remember everthing between sessions. 

So I decided to create a `.context` folder, where each inner subfolder was own Jira ticket being worked on. Inside that folder I placed each relevant piece of information, which was used to successfully close that given Jira ticker. This included the Jira ticket's description, extracted infromation from personal conversations with engineers about the core issue, what was already tried, different findins and so on.

At the time that I was working, I guided my agents to this folder using global and local AGENTS.md and CLAUDE.md, but now I decided to go back to this system and turn in into a proper skill. Basically, it structurises the process of working with this `.context` folder, describe how files should be named, what to place in each file, how to navigate and work with context files, transforming my prevously usual AGENTS.md-contained system into a proper production-ready skill, which can be used for engineers alike to help them work on big and complex tasks requiring lots of external information.

## Example

The repository and skill includes [a complete real-life example](skills/context-manager/examples/FW-1427/) of the skill's workflow being used. 

The materials were taken from a **real BIOS project**, where a single Jira ticket was worked on for ~3 months, with complex research, many attempts and incremental solution discovery. It contains technical investigation, chronology, failed attempts, findings, conversations, and handoff state, providing a practical showcase of how the skill can be used throughout a complex real-world project.

> Do note, that all sensetive and private information from the source folder as removed or anonymised to comply with company's policies. Instead, in those places the example provides renamed terms/namings/etc. and placeholder files (like logs), but all the technical depth and core issue were preserved.

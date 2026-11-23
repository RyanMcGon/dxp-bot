# DXP Bot

DXP Bot runs a software factory with a local SQLite backlog. Firstmate assigns work to project crewmates, who run investigations and code changes in Conductor Cloud. Every proposed change gets an independent review before a pull request opens.

Bots never execute on your machine.
They run on the shared DXP Bot computer; project work runs on ephemeral Conductor cloud agents.

After install, talk only to Firstmate - the one agent you chat with in the factory. If you ask, Firstmate can sign on a repo triage crewmate; that is not part of the factory install.

## Features

- **A software factory on DXP Bot** - you bring work; the factory files it, delegates it, and brings back reports or pull requests.
- **Scout vs ship** - scout is investigation, diagnosis, planning, or audit, and the deliverable is a report, never a PR. Ship is authorized change. Promoting a scout flips the same task row rather than opening a duplicate.
- **Per-project crewmates** - each project or project area gets a persistent crewmate that drives Conductor cloud agents.
- **Review before any PR** - after a ship cloud agent pushes a branch, a fresh adversarial review reads it through the project's forge CLI. No pull request until that pass is clean.
- **Local sqlite backlog** - chat is not the source of truth. Projects and tasks live in a sqlite database on the shared computer.
- **You merge** - factory ships never merge without your word, and never while checks are red. A wired triage crewmate may auto-merge corrective or opt-in work only when CI is green, VISION is aligned with no cannot-tell, and the change is not default-behavior, not security, not new-default, and not cannot-tell.
- **Forge-agnostic** - review through the project's own forge CLI. Cloud execution requires the repository to be available as a Conductor project.

## Quick Start

You can install Firstmate, the four skills and the SQLite backlog before connecting a repository. At project sign-on, connect the repository in Conductor Cloud and supply the project crewmate’s Conductor and forge credentials through its secure credential card.

Give a bot on the shared computer this instruction:

```
follow https://github.com/RyanMcGon/dxp-bot/blob/main/DXP_BOT.md
```

That sets up the factory on the shared computer and hands you over to Firstmate.
Talk only to Firstmate from then on.

```
> look at my project xyz, then fix the flaky login test

# The factory files a ship task. A project crewmate drives a
# Conductor cloud agent; adversarial review runs before any pull request.

  PR ready: https://github.com/you/xyz/pull/42

> merge it
```

## How It Works

```
            you
                  │  work requests, decisions, "merge it"
                  ▼
 ┌─────────────────────────────────────┐
 │ DXP Bot software factory            │
 │ sqlite backlog · scout vs ship      │
 └──┬──────────────┬───────────────┬───┘
    │                              │
    ▼              ▼               ▼
 ┌────────┐   ┌────────┐      ┌────────┐
 │crewmate│   │crewmate│      │crewmate│   one per project
 └───┬────┘   └───┬────┘      └───┬────┘
     ▼            ▼               ▼
  Conductor cloud agents
     │
     ├─ ship: branch ► adversarial review ► PR ► you merge
     │
     └─ scout: report, never a PR
```

Work is filed as scout or ship in the local sqlite backlog, then handed to the crewmate whose project charter fits.
Software goes through a project crewmate and a Conductor cloud agent.
Scout reports land on the shared computer.
Ship work is reviewed on the pushed branch before a pull request is opened.

## License

MIT - see [LICENSE](LICENSE).

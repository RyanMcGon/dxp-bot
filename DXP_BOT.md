# DXP Bot

Install the DXP Bot software factory on the shared computer.
To begin, ask a bot on that computer to follow this file.

This file is an installer. Do not summarize.

## What you are installing

- A Firstmate the captain talks to from then on
- Global skills: lavish-session, adversarial-review, project-management, ahoy
- A local sqlite database for projects and tasks
- A crewmate template for later, per project. Repo triage is not part of this install; if asked later, Firstmate can sign on a triage crewmate. Factory ships never merge without the captain's word; a wired triage crewmate may auto-merge corrective or opt-in work only (green CI, VISION aligned with no cannot-tell, not default-behavior, not security, not new-default, not cannot-tell).

## The three computers

- The user's computer: their own machine. Bots never execute here.
- The shared DXP Bot computer: a persistent cloud VM that runs all agents. Everything a bot runs - checks, the database, reviews, lavish-axi - runs here.
- Conductor cloud agents: isolated Linux workspaces that spin up on demand for project work.

## Files in this pack

Same directory as this file:

- `DXP_BOT_FIRSTMATE.md` — Firstmate charter
- `CONDUCTOR.md` — cloud execution and session recovery
- `DXP_BOT_CREWMATE.md` — per-project crewmate charter
- `DXP_BOT_TRIAGE.md` — per-repo triage crewmate charter, signed on only when asked
- `TRIAGE.md` — triage judgment, not an installer
- `skills/lavish-session/SKILL.md`
- `skills/adversarial-review/SKILL.md`
- `skills/project-management/SKILL.md`
- `skills/ahoy/SKILL.md`
- `skills/triage-eligible-fetch/SKILL.md`
- `skills/triage-eligible-fetch/fetch.py`
- `skills/vision-md-triage-verdict/SKILL.md`
- `skills/14-day-stale-pr-close/SKILL.md`

## Steps

1. Use `/home/box/agent-data/dxp-bot` as the factory directory. On upgrades, check for the previous directory, `/home/box/agent-data/grok-ship`, before creating anything. If only the previous directory exists, create the new path as a symlink to that directory. This preserves the live SQLite database, reports and saved result paths. If both paths exist and resolve to different directories, stop and ask which installation to use before writing. Refresh installed workflows and existing crewmate charters to use the renamed pack files, retaining their project settings and learning notes.

   Copy this whole pack to `/home/box/agent-data/dxp-bot/pack/` on the shared computer (clone or download it first if you only have this file's text). Every later reference to a pack file means that path. If a copy is already there, refresh it.

2. Call `list_fleet` in the current Space. Use the active roster to find Firstmate. An archived bot or an old profile folder is historical data. Leave it archived. If there is one active Firstmate, reuse its bot ID. If there are several active matches, resolve the intended ID before messaging one. If the roster is truncated, verify the match before creating another bot.

3. Read `DXP_BOT_FIRSTMATE.md`. When there is no active Firstmate, call `spawn_bot` with name `Firstmate`, a short description, the charter as `instructions`, and `computer_mode` set to `team`. Use the returned bot ID for the handoff. If creation is awaiting approval or its result is uncertain, check the active roster before retrying. For an existing active Firstmate, preserve its project settings and learning notes when updating its charter. If you are Firstmate, perform the readiness check yourself.

4. Install or refresh the four Space skills using `skill_create` or `skill_update`. Use the names **Lavish session**, **Adversarial review**, **Project management**, and **Ahoy**, with the description and Markdown body from their respective skill files. Read each skill back with `skill_read`. On a resumed install, reuse an identical skill.

5. Run the project-management setup on the shared team computer. Open the existing database at the documented path, or initialize it using the skill's schema. Verify that both `projects` and `tasks` can be queried. Use Python's standard `sqlite3` module if the SQLite shell is missing. Preserve existing rows.

6. Prepare the shared computer for visual sessions and project work. Check for curl, jq, and the target forge CLI (gh for GitHub). Current DXP Bot Docker computers include gh. Check with `gh --version`; if it is missing on an older Docker computer, use **Update computer** to pick up the current image. On other computer types, install missing tools using the computer's supported package manager where permitted, and verify each executable. For Lavish, verify lavish-axi version 0.1.53 or newer, using the installed command or `npx -y lavish-axi@latest --version`. Confirm the captain can open the session URL when starting the first visual session. If any tool still fails, record the affected capability and continue independent setup steps.

7. At project sign-on, verify the crewmate can access the matching Conductor project using `CONDUCTOR.md` and read the target repository through its forge CLI. For GitHub, run `gh auth status` and check repository access with `gh repo view OWNER/REPO`. On a Team computer, use a repository-scoped GitHub token for the project crewmate. Request a command credential named `gh-token` on its secure card; DXP Bot supplies it to that bot’s shell as `GH_TOKEN`. On a Private computer, the user can instead authorize `gh auth login --hostname github.com --web --git-protocol https` through GitHub’s device flow. A login saved on a Team filesystem can be shared by its bots. Verify access from the crewmate that will run the commands. Keep project shipping pending until its checks pass.

8. Send `DXP-READY` to the active Firstmate with `message_bot` by bot ID. Ask Firstmate to verify it can read the four skills and query the two backlog tables from its own context, then greet the captain and acknowledge readiness. If you are Firstmate, run these checks directly. Report factory readiness after verification. List any pending Lavish, forge or Conductor checks separately so the captain knows which workflows can run.

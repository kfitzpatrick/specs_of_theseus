# Usage

Specs of Theseus ships as a plugin called `theseus` for Codex, Cursor, and Claude Code, with a skill for each stage. You work with the agent the usual way, in plain words. The skills tell 🧞 the agent what to do at each step, and make it stop wherever a person decides.

## Install

### Claude Code

In Claude Code, in the project you want to use the method on:

```
/plugin install theseus --marketplace infews/specs_of_theseus
```

That needs Claude Code 2.1.275 or later. On an older version, add the marketplace first, then install:

```
/plugin marketplace add infews/specs_of_theseus
/plugin install theseus@specs-of-theseus
```

Then start a new session so the skills load.

**For the whole team:** install from a terminal with project scope. The plugin is then recorded in the project's `.claude/settings.json`, and everyone who opens the project in Claude Code is offered it.

```sh
claude plugin marketplace add infews/specs_of_theseus --scope project
claude plugin install theseus@specs-of-theseus --scope project
```

**To update** to the latest version:

```sh
claude plugin marketplace update specs-of-theseus
claude plugin update theseus@specs-of-theseus
```

### Codex

From a terminal, add the remote marketplace, then install:

```sh
codex plugin marketplace add kfitzpatrick/specs_of_theseus
codex plugin add theseus@specs-of-theseus
```

Then start a new chat in the project you want to use the method on.

**To update** to the latest version:

```sh
codex plugin marketplace upgrade specs-of-theseus
codex plugin add theseus@specs-of-theseus
```

Codex uses the catalog in `.agents/plugins/marketplace.json`. See [Codex plugin marketplaces](https://developers.openai.com/plugins/build/plugins#add-a-marketplace-from-the-cli).

### Cursor

From a terminal, add the remote marketplace:

```sh
agent plugin marketplace add https://github.com/kfitzpatrick/specs_of_theseus
```

Then run `agent`, type `/plugin`, open the **Marketplace** tab, choose `theseus`, and install it. Choose user scope to use it across projects, or project scope for the current project. User installations also sync to the Cursor IDE. See [Cursor's installation instructions](https://forum.cursor.com/t/unable-to-find-a-cli-command-to-install-a-cursor-plugin-after-adding-its-marketplace-repository/166016).

**To update** the marketplace:

```sh
agent plugin marketplace update specs-of-theseus
```

A team can also import the repository through **Dashboard → Plugins & MCPs → Team Marketplaces → Add Marketplace → Import from Repo**. Team marketplaces require a Teams or Enterprise plan. See [Cursor team marketplaces](https://cursor.com/docs/plugins#team-marketplaces).

## The Skills

| Skill | Stage | Runs when |
| --- | --- | --- |
| `theseus:specs-of-theseus` | Any | You ask "what's next?", or you're not sure where the work is. It works out the stage and hands off. |
| `theseus:adopt-project` | [Adopting an Existing Project](6_adopting.md) | The project has code but no `docs/evals/`. |
| `theseus:product-planning` | [Product Planning](1-product-planning.md) | Writing an epic, breaking it into stories, or ranking the backlog. |
| `theseus:update-evals` | [Update Evals](2-update-evals.md) | Starting the next story: writing its evals and their failing tests. |
| `theseus:redesign-system` | [Redesign System](3-redesign-system.md) | A story has evals but no implementation plan. |
| `theseus:develop-story` | [Development](4-development.md) | A story has reviewed tasks to build, or is ready to tag. |

[Acceptance](5-acceptance.md) has no skill. It's your team's own deploy and review process.

## How to Use the Skills

- **You don't have to name them.** Say what you want, such as "break the checkout epic into stories" or "let's start the next story", and the agent picks the matching skill.
- **You _can_ name one** to be explicit: `/theseus:update-evals` in Claude Code. In Codex, select `update-evals` from the skill picker. In Cursor, type `/` and search for `update-evals`.
- **When in doubt, ask "what's next?"** 
  - The entry skill reads the project's files, says which stage the work is in and why, and hands off to the next skill.
  - "Next: **Update Evals** (`theseus:update-evals`)". The skill announces this way, so you always know which one is running.
- **The files are the record.** 
  - Progress lives in the epic and story files under `docs/` and in git tags, not in the chat. 
  - You can stop at any point and pick up in a new session. See [Where Things Live](where-things-live.md).

## Getting Going

**A new project:** start with Product Planning. 🧑🏾‍⚖️ The product manager chooses the first epic. Then can work with the agent to write it up and break it into stories.

**An existing project with code but no evals:** say "adopt this project". The `adopt-project` skill surveys the code, records the project's tools, marks subsystems, and writes an adoption epic with the product manager. See [Adopting an Existing Project](6_adopting.md).

**Tools:** the [Development](4-development.md) loop needs a command for each step (tests, property tests, mutation tests, controls and checks). They live in the project's `CLAUDE.md` or `AGENTS.md`. If one is missing, the agent asks and records your answer there. See [Tools](working-agreements.md#tools).

## A Story, Start to Finish

| Step | You say | 🧞 The agent | It stops for |
| --- | --- | --- | --- |
| Plan | "Break down the checkout epic." | Drafts small stories with Gherkin acceptance criteria and proposes an order. | 🧑🏾‍⚖️ The product manager to edit and rank the stories. |
| Update Evals | "What's next?" | Takes the top story, writes or changes its evals as EARS requirements, and writes a failing test for each. | 🧑🏼‍💻 The developer to review the evals and tests. |
| Redesign System | "Plan it." | Plans the smallest change, looks for a subsystem worth regrowing, and writes tasks. | 🧑🏼‍💻 The developer to review the tasks. |
| Development | "Go." | Works through the tasks test-first, runs the full eval suite, mutation tests and checks, then tags the story `story/<epic>/<story>`. | Nothing, unless it raises an issue. 🧑🏼‍💻 The developer oversees. |
| Acceptance | n/a| n/a | 🧑🏾‍⚖️ The product manager accepts or rejects the story on working software according to team practices. |

If the product manager rejects a story, tell the agent why ("the product manager rejected the login story: it should remember the email"). It goes back to Product Planning to be re-ranked, and usually its evals get fixed.

## The Agent Can Stop and Ask for Help

When the agent is stuck, or finds something it can't settle on its own, it raises an issue. It writes the issue into the story's `## Issues` section (or the epic's `## Open Questions`), stops, and tells you in the session. Work on that story stops until a person writes an answer under the issue and marks it resolved. You can answer in the file, or tell the agent the answer and have it write it down. See [Issues](working-agreements.md#issues).

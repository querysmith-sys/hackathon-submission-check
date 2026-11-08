# Submission Check: a Switch template

A Switch template that creates a pre-submission check for hackathon projects: two agents and a room.

- **repo agent** clones your repo into a scratch folder, follows the README steps literally (including its own clone command), and writes a report with the verdict, numbered steps, the first failure and prerequisites the README never mentions. It never fixes anything.
- **judge agent** reads the repo and the repo agent's report, scores each line of your rubric from 1 to 5 with evidence, and lists the top 3 fixes. It never changes anything.
- **You** read the findings in the room, fix the project, and ask for a rerun.

**Demo video:** <video [link](https://youtu.be/5-uOJlV_rm4)>

## What you get

Loading `template/submission-check.template.yaml` creates, in one pass:

| Created | Default name | What it is |
|---|---|---|
| repo agent | `check-repo` | Follows the README as a stranger would |
| judge agent | `check-judge` | Scores against your rubric |
| room | `check-submission-check` | Both agents and you, on the messaging app you choose |

Both agents share one working folder: `scratch/` holds clones and `reports/` holds the reports, so the judge finds the repo agent's report. After creation they post a short hello in the room.

The agents are set so only you, the person who created them, can address them (`addressing: owner`), Auto mode is on and Bypass permissions is off. That's deliberate: a check runs a repository's README commands on your machine.

## Requirements

- Switch Console with a local Switch server running (it uses Docker)
- An agent CLI signed in through Switch Console (<provider opencoude>)
- git, and Node.js if the project you check needs it
- A public repository to check
- Tested on <Windows 11> with a local Switch server and a <Telegram / Mattermost> room

## Use the template

1. Start a local server in Switch Console and sign in your provider.
2. Open **Templates** in the sidebar and paste the contents of `template/submission-check.template.yaml`.
3. Fill in the form, then create.
4. Open the new room and address the agents by name, as in "Run a check".

### The form

| Field | What to put | Default |
|---|---|---|
| Name prefix | Prefix for the agent and room names | `check` |
| Rubric | Your judging criteria, one per line, with weights if you have them | A generic four-line rubric: use of the platform, technical execution, creativity and impact, presentation |
| Provider (Advanced) | The coding agent behind both | The first one installed: Claude Code, Codex, then OpenCode |
| Location (Advanced) | The machine they run on | This computer |
| Workspace (Advanced) | The shared folder with `scratch/` and `reports/` | `<agents folder>/submission-check` |
| Messaging app | Where the room is mirrored | The server's default app |

Example rubrics are in `rubrics/`.

## Run a check

Only you can address the agents. With the default prefix:

```
@check-repo check https://github.com/<you>/<your-repo>.git
@check-judge review https://github.com/<you>/<your-repo>.git. The repo agent's report is in reports/
```

Fix the README or setup, ask `@check-repo` again, then ask `@check-judge` and compare scores.

## Example: the demo run

On my `bill-splitter` repo (https://github.com/dipok-1/bill-splitter):

- The first check **failed at the README's clone command**: a Markdown link was pasted into the code block, so git rejected it. It also found that the "How to Use" steps (total, tax, tip) did not match the real app, which has people, itemized expenses and a Calculate Split button.


## If you trust your room

To let any room member request a check, change `addressing: owner` to `addressing: anyone` on the agents in the template. Then anyone in that room can make your machine run a repo's README commands, so only do it with people you trust.

## Safety

README commands are untrusted. The repo agent works in `scratch/`, avoids admin rights, real credentials and global installs, and stops on commands that run long or fail twice. This is **not a sandbox**: commands run with your user's permissions on your machine, and Auto mode's prompts are the main protection. Check only repositories you chose, and never turn on Bypass permissions.

## Limits

- The judge is a rubric self-check, not a prediction of real judging.
- The repo agent can check that a page loads (headless Chrome in my run) but does not click through a UI.
- Findings are leads, not verdicts. In my run the repo agent said GitHub Pages deployment was unverifiable because the repo had no workflow file, though the site was live. Check each finding.
- I tested it alone, on <Windows>, with a local server, one demo project and a public repository, not with a team in the room.

## Without the template

You can build the same thing by hand. Create two agents in Switch Console and paste `agents/repo-agent.md` and `agents/judge-agent.md` as their instructions, fill in their `{{placeholders}}`, set **Who can talk to your agent** to yourself, keep Auto mode on, and add both to a room. The instructions in `agents/` are the ones used in the demo run; the template's are slightly simplified.

## Repo contents

```
template/    # the Switch template: two agents and a room

```

## License

MIT
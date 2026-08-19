# skills
Collection of agent skills for IE work

## Installation

Skills can be installed through the command line with GitHub CLI:

`gh skill install skills/test-modules-after-migration --from-local` (Install from local repo)
`gh skill install insightsengineering/skills test-modules-after-migration` (Install from online repo)

If required, set the `--dir` flag on `gh skill install` to specify the folder of installation depending on your coding agent.
Skills can be installed at project level (i.e `.claude` or `.copilot` folder) or at user level (`~/.claude`)

If GitHub CLI is not available, clone the repository and copy the skill folder you need into your project or user agent folder.

## New skills

When creating a new skill, please follow [agentskills](https://github.com/agentskills/agentskills) as a standard reference.
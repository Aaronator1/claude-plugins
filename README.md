# Aaron's Claude Code plugins

A Claude Code plugin marketplace. It has one plugin so far.

## Install

In Claude Code:

```text
/plugin marketplace add Aaronator1/claude-plugins
/plugin install expert-panel@aaronator1-plugins
```

The repository is private, so you need read access to it and a GitHub sign-in that Git can use (for example `gh auth login`).

## expert-panel

A read-only review of a product's UI by a panel of domain experts you choose. Each expert is its own agent, and they review in parallel. You get one merged report: verdicts, findings by severity, disagreements, and what to fix first.

| Command | What it does |
| --- | --- |
| `/expert-panel:review` | Pick who joins the panel, gather the screens, run the review, and merge the reports. Supports a re-review of earlier findings |
| `/expert-panel:new-expert` | Create a new expert or edit one, and save it for you, for the project, or for everyone who installs the plugin |

### Built-in experts

Each expert is one file in [`plugins/expert-panel/experts/`](plugins/expert-panel/experts/):

- `ceo`: revenue cycle executive
- `operations-manager`: revenue cycle operations manager
- `billing`: billing expert (claims and coding)
- `collections`: collections expert (A/R, underpayments, denials, posting, patient balances)
- `ux-designer`: UI/UX design expert

### Your own experts

`/expert-panel:new-expert` writes the file for you. You can also create one by hand in either of these places:

- `~/.claude/expert-panel/experts/<name>.md`: yours, in every project
- `.claude/expert-panel/experts/<name>.md`: the project's, shared with everyone who works in it

If an expert has the same `name` as a built-in one, it replaces the built-in expert. Copy any built-in file to see the format.

### Data

Only review synthetic data, or screens that are otherwise safe to share. Never give the panel real patient, claim, payer or clinical data.

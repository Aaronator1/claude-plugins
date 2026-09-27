---
name: new-expert
description: Create a new expert for the expert panel, or edit one. Use when the user wants to add a reviewer, persona or expert to the panel, or change an existing expert's needs.
argument-hint: "[who the expert is, e.g. 'a practice manager at a client clinic']"
---

# Create or edit a panel expert

An expert is one Markdown file. `/expert-panel:review` reads every expert file and offers each one when the user picks the panel.

## 1. Understand the expert

Start from whatever the user said. Ask only for what's missing, in one round, with `AskUserQuestion` where there's a clear choice:

- **Who they are:** role and expertise. This becomes the `persona`, written the way a briefing would introduce them, e.g. "a practice manager at a small rural clinic who approves what the billing company sends".
- **The one question they ask of software like this.**
- **What they need:** four to seven needs. If the user gives only a rough idea, draft the needs yourself in the style of the built-in experts (`${CLAUDE_PLUGIN_ROOT}/experts/*.md`): a bold label, then a colon, then what exactly they need to see or do.
- **Focus:** the screens or areas they should look at most.
- **Extra questions:** anything specific this expert should answer beyond their needs. These are optional.

To edit an expert, read its current file first, show it to the user, and change only what they ask.

## 2. Choose where it lives

Ask where to save it, unless the user already said:

- **Just for me** (the default): `~/.claude/expert-panel/experts/<name>.md`, available in every project.
- **This project only:** `.claude/expert-panel/experts/<name>.md`, which is committed with the project so the whole team gets it.
- **Everyone who installs the plugin:** add it to the plugin's `experts/` folder in its GitHub repo, through a pull request or a commit if the user owns the repo. Say that other users get it when they next update the plugin.

An expert with the same `name` as a built-in one replaces it wherever the new file is found. Use that to customise a built-in expert, and warn the user when a name clashes.

## 3. Write the file

Use a lowercase, hyphenated `name` that matches the file name.

```markdown
---
name: practice-manager
title: Client practice manager
question: Is the billing company getting our money in, and what do they need from us?
persona: a practice manager at a small rural clinic who approves what the billing company sends
words: 700
---

## Needs
- **Label:** what exactly they need to see or do.

## Focus
The screens or areas to look at most.

## Extra questions
- A specific question for this expert.
```

`words` is the report length. Use 700 for most experts and 800 for someone who reviews every screen.

Show the user the finished file before saving it. After saving, tell them where it is and that it will be offered the next time they run `/expert-panel:review`.

Never put real patient, claim or other personal data into an expert file. An expert describes a point of view, never a record.

---
name: review
description: Review a product's UI with a panel of domain experts the user chooses. Use when the user asks for an expert panel, a panel review, or a UI review from several expert points of view, or for a re-review of earlier panel findings.
argument-hint: "[what to review, e.g. a URL or a screens folder] [--rereview <previous report>]"
---

# Expert panel review

Run a read-only review of a product's UI by a panel of experts the user picks. Each expert is a separate agent with its own persona and needs. They work in parallel, without seeing each other's reports, and you merge what they find.

**All review material must be synthetic or otherwise safe to share. Never give the panel real patient, claim, payer, clinical or other personal data.** If the screens might contain real data, stop and ask the user before you continue.

## 1. Find the experts

Read the expert files from these three folders. If two share a `name`, the first one found wins:

1. `.claude/expert-panel/experts/*.md` in the current project: the project's own experts
2. `~/.claude/expert-panel/experts/*.md`: the user's own experts
3. `${CLAUDE_PLUGIN_ROOT}/experts/*.md`: the experts that ship with this plugin

Each file has frontmatter (`name`, `title`, `question`, `persona`, `words`) and three sections: `## Needs`, `## Focus` and `## Extra questions`. If a file is missing a field, tell the user which one and leave that expert out.

## 2. Let the user choose who joins

Show the user every expert found: title, question, and where it came from (project, yours, or built in). Then ask who joins, with `AskUserQuestion` set to `multiSelect: true`.

- A question holds at most four options. With more than four experts, split them across up to four questions, grouped sensibly (for example, "Business and operations" and "Specialists and design").
- Add a note that they can choose **Other** to describe a one-off expert for this review only. You then write that expert's needs in the same shape and show them to the user before the review starts. Don't save a one-off expert. Suggest `/expert-panel:new-expert` if they want to keep it.
- If the user named experts in their request, such as "billing and UX only", skip the question and confirm the selection in one line.

## 3. Gather the materials

The panel reviews screenshots and page text, one pair per screen, named `NN-name.png` and `NN-name.txt`.

- If the user gave a folder of screens, use it.
- If the project has a screen capture command, run it. For example, the Automated Revenue Manager uses `npm run screens -- <label>`, which writes to `reports/screens/<label>/`.
- Otherwise, with a running app and a browser tool, capture each route yourself into a scratch folder: a screenshot at 1440px wide plus the page's visible text.
- If none of these is possible, ask the user how to get the screens. Don't review from source code alone.

Also note the app's URL, if it's running, and the source folder, so reviewers can check a claim.

## 4. Brief each expert

Start one agent per selected expert, all in a single message so they run in parallel. Use a general-purpose agent. Build each prompt from the expert's file:

```text
You are {persona} on a {N}-person expert panel reviewing the UI of "{product}",
{one-sentence description: who uses it, what it does}. All data is synthetic.
This is a READ-ONLY review: do not modify any file anywhere, and do not run
seeds, tests, migrations or git.

Materials: screenshots and page text for {count} screens in {folder}. File
names are NN-name.png / NN-name.txt. Read the .txt for every screen (they are
short) so you understand the whole information architecture, then look closely
at the screens that matter most to you: {Focus}.
{If running: The app is at {url}; you may fetch other pages.}
{If available: Source is in {path} if you need to check something.}

The question you ask of software like this: {question}

Your needs:
{Needs}

Also answer:
{Extra questions}

Deliver (max ~{words} words, plain text, no preamble):
1. VERDICT in one sentence.
2. WHAT WORKS: up to 4 bullets, each naming the screen file.
3. WHAT FAILS YOU: up to 7 findings, ordered by severity (Critical/High/Medium),
   each: screen file, what's wrong, why it matters to you, the fix.
4. IDEAL SHAPE: your landing screen and daily flow, how you get from a headline
   to the thing behind it, and what you would change in the navigation.
5. One quotable line (under 20 words) in your voice.
Be specific and blunt; cite the actual numbers and labels you saw on screen.
```

### Re-review mode

If the user asks for a re-review, or passes `--rereview` with an earlier report, add each expert's own earlier findings to their prompt. Ask them to mark each finding **fixed**, **partly fixed** or **not fixed**, cite the screen that shows it, and then list anything new.

## 5. Merge the reports

When every expert has reported, write one synthesis:

1. **Bottom line:** two or three sentences on what the panel agrees on.
2. **Verdicts:** each expert's one-sentence verdict and quotable line.
3. **Findings by severity:** Critical first. Merge duplicates, and name every expert who raised each one.
4. **Where they disagree:** state each conflict (for example, density wanted by specialists against simplicity wanted by the designer) and recommend a side.
5. **What to do first:** the three to five changes that answer the most findings.

In a re-review, add a table with one row per earlier finding showing its status now.

Give the user the synthesis. If they want to share it, offer to publish it as a page or save it as a file. Keep the full expert reports available in case they ask for one.

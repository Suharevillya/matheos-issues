# Working on `.mth` calculations with Claude

A `.mth` file is plain text: Markdown prose and ```` ```math ```` blocks. Claude can write, convert,
check and fix one directly, the way it works on any text file. This page shows the two ways to
set that up and the prompts that work. The authoring rules Claude follows live in one file,
[SKILL.md](https://github.com/Suharevillya/matheos-issues/blob/main/SKILL.md); everything below is
about putting that file in front of Claude and closing the loop with a check.

## What you need

- A Claude account: [claude.ai](https://claude.ai) (chat, desktop app) or
  [Claude Code](https://claude.com/claude-code) (terminal, works in a folder of files).
- The editor at <https://matheos.fly.dev/app/> to look at the report and the verdicts.
- Optional, for the terminal route: Node.js 20 or newer and the Matheos command line,
  `npm install -g @matheos/cli`.

## Route 1: chat, no installation

1. Open claude.ai and make a Project called *Matheos*. In its knowledge, add `SKILL.md`
   (download it from the link above, or paste its text). Every chat in the project now knows
   the grammar, the units and the mistakes to avoid.
2. Ask for the calculation. Give the inputs, the code clauses and what the checks are:

   > Write a `.mth` calculation for a simply supported IPE 300 beam, S355, span 6 m, uniform load
   > 25 kN/m, checks for bending and shear to EN 1993-1-1 §6.2, inputs marked as parameters.

3. Copy the answer into the editor (new document, paste). The report renders on the right;
   errors show on the line, verdicts as badges.
4. Paste any error text back to Claude: *"line 14 says `dimension mismatch`, fix it"*. Two or
   three rounds are normal for a converted spreadsheet.
5. Save from the editor: a file on your machine, or your GitHub template library.

The same works in the Claude desktop app, and with a one-off chat if you attach `SKILL.md` to
the first message instead of a project.

## Route 2: Claude Code in a folder of files

Claude Code edits files and runs commands itself, so the loop closes without copying text.

```sh
npm install -g @matheos/cli
mkdir -p calcs/.claude/skills/matheos-authoring
matheos guide > calcs/.claude/skills/matheos-authoring/SKILL.md
cd calcs
claude
```

`matheos guide` prints the same `SKILL.md`; placing it under `.claude/skills/` makes Claude Code
load it whenever a `.mth` file is in play. Then ask:

> Convert `old/purlin.xlsx` to `purlin.mth`, keep the same inputs and checks, mark the inputs as
> parameters, and run `matheos check` until it is clean.

Claude writes the file, runs `matheos check --json purlin.mth`, reads the errors and the failed
checks, fixes the document, and repeats. `matheos render purlin.mth` makes the PDF (first run
downloads a Chromium with `matheos install-chrome` if none is found). Open the `.mth` in the
editor when you want to see it live.

Other agents (Codex, Cursor, Gemini CLI) take the same `SKILL.md`; put it where that tool reads
its instructions (`AGENTS.md`, `.cursor/rules`, a system prompt).

## Prompts that work

- *Convert* — "Here is a Calcpad script / an Excel sheet / a PDF of a hand calc. Write the same
  calculation as `.mth`. Keep the section order and the clause references." Attach the file.
- *Template* — "Make this a template: every number an engineer changes becomes a parameter with a
  caption, and the options where the sheet had a drop-down (`curve = "b" ? ["a", "b", "c"]`)."
- *Review* — "Check `beam.mth` against EN 1993-1-1 §6.3.2.2: are the buckling curve and the
  imperfection factor right for a rolled IPE about the minor axis?"
- *Extend* — "Add a deflection check, limit L/250 under the characteristic combination, as a
  new section before the summary table."
- *Explain* — "Why does `M_cr` come out 20 % lower than the Calcpad version?" Paste both.

## What stays with you

Claude types fast and knows the grammar; it is not the engineer of record. Before a report
leaves the office:

- Read every `check`. A green badge means the inequality holds for the numbers in the file, not
  that the right inequality was written.
- Verify clause numbers and factors against the code text, especially national annex values.
- Look at the units on every input line. `kN/m2` without `^` is a classic (the check catches
  most of these, not all of them).
- Compare one result by hand or against the old sheet.

## When something is wrong with Matheos

Open an issue at <https://github.com/Suharevillya/matheos-issues/issues> with the `.mth` text
that shows the problem and what you expected. Claude can draft the issue text for you.

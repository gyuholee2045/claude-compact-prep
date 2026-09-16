# compact-prep

[한국어](README.md) | **English**

A Claude Code skill that writes down what your session decided before `/compact` wipes the details, then restores context from those files afterwards.

## Why

`/compact` is lossy. The things that disappear first are the ones you notice when they're gone: exact numbers, final copy, alternatives you already rejected, corrections you gave halfway through. After compaction the session asks the same question again, or quietly uses a different value than the one you settled on.

This skill has one bar: **a fresh session that reads only these files shouldn't re-ask a settled decision or write a number differently.**

## Install

Drop the folder into your skills directory.

```bash
git clone https://github.com/gyuholee2045/claude-compact-prep.git ~/.claude/skills/compact-prep
```

Windows (PowerShell):

```powershell
git clone https://github.com/gyuholee2045/claude-compact-prep.git "$env:USERPROFILE\.claude\skills\compact-prep"
```

To use it in a single project only, clone into that project's `.claude/skills/` instead. Open a new session after installing so the skill gets picked up.

## Usage

| When | Type |
|---|---|
| Right before compacting | `/compact-prep` |
| Save, plus a note for the next session | `/compact-prep start with section 4 copy next` |
| After compacting, restore and keep going | `/compact-prep restore` |

The skill also triggers on plain requests such as "save everything before we compact" or "compaction's done, let's pick up where we left off".

## What gets saved

It creates `session-memory/` in your working directory and splits notes by category. Files with nothing to record aren't created.

| File | Contents |
|---|---|
| `PROGRESS.md` | Current state, work in progress and the next step, blockers, files touched. Restoration starts here |
| `DECISIONS.md` | What was decided and why, rejected alternatives and why they lost, how one thing evolved from v1 to v3 |
| `SPECS.md` | Numbers, color codes, fonts, versions, paths, final copy verbatim, numbering schemes |
| `DESIGN.md` | Tone, layout principles, type/color/spacing rules, references, don'ts, where the mockups live |
| `STYLE.md` | How you like to work, what you dislike, your corrections quoted |

## Recording rules

Precision is the whole point. These rules keep what summaries tend to blur.

- **Append, don't overwrite.** Existing files are read first and built on.
- **Verbatim values.** No "about 24px" or "bluish". The value from the conversation goes in as-is.
- **Numbering is never reshuffled.** Your own schemes ("section 3-2", "option B") keep their exact form. IDs the skill assigns (`D-`, `S-`) never change once given, since other entries point to them.
- **Reversed decisions are struck through, not deleted.** `~~D-004~~ → superseded by D-011`. What changed and why is the part you'll miss most later.
- **Unsure means flagged.** If a value showed up two different ways, it's marked `[needs confirmation]` and you get asked, instead of a guess.
- **The whole conversation gets scanned**, not just the last few turns. Early decisions are the first to vanish from a summary.
- **No secrets.** Keys and tokens are recorded by location only.

## Make restoration automatic

Add one line to your project's `CLAUDE.md` and you won't need to type `restore`, because `CLAUDE.md` is reloaded after compaction.

```markdown
- On session start or right after compaction, read session-memory/PROGRESS.md first and follow the recorded decisions and specs.
```

The first time the skill saves in a project, it asks whether to add this line. It only adds it if you say yes.

## Pair it with the compact command

After saving, the skill suggests a `/compact` command you can paste, so the summary points back to the files.

```
/compact Decisions and specs are recorded in session-memory/. Keep D-/S- IDs and file paths intact in the summary, and prioritize in-progress work and next steps.
```

## Language note

The skill instructions are written in Korean. Claude follows them fine regardless of the language you work in. If you want the saved notes in English, just say so when you run it, e.g. `/compact-prep write the notes in English`.

## License

MIT

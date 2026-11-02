# Vault conventions

This repo is Nasif's Obsidian vault (`.obsidian/` at the root) for a self-study CS curriculum. Read this before touching any file.

## Hard rules

- **Never rewrite Nasif's words.** Move, rename, add frontmatter, fix link targets — nothing else. Where a note needs his own words, leave a `TODO (Nasif):` marker.
- **Never move or rename files while Obsidian is open.** Obsidian rewrites links itself and will race you. Ask him to close it first.
- Never delete a frontmatter property. Add or merge only.
- Progress and status live in Notion, not here. Don't write status text that will go stale.
- `media/` is attachments only. Leave its files where and as they are.

## Layout

```
00-home.md         dashboard (embeds Bases views)
01-modules/        one hub note per module + semester-01.md
02-sources/        one note per chapter / episode / article
03-concepts/       atoms, flat, never per-module subfolders
04-questions/      one q- file per question asked to a human or AI; weak-spots.md log
05-syntheses/      cold answers to module questions
06-assignments/    exams, retests, code
99-meta/           templates/, bases/, writing-style-guide.md
media/             attachments
_archive/          retired material, kept for history (no frontmatter required)
```

The module is a **property**, not a folder: concepts cross modules.

## Frontmatter

| Folder | Required properties |
|---|---|
| `01-modules/` | `type: module`, `module: <n>` (`0` = semester overview), `status: closed\|active\|planned` |
| `02-sources/` | `type: source`, `module`, `kind: book\|video\|article\|lecture` |
| `03-concepts/` | `type: concept`, `module` (where first learned), plus existing `tags` (depth: `deep`/`medium`/`story`) and `source` |
| `04-questions/` | `type: question`, `module`, `status: open\|answered\|promoted`, `asked`, `ai`, `source`, `concepts` (optional `follows`) |
| `05-syntheses/` | `type: synthesis`, `module`, `question`, `score`, `last_recalled` |
| `06-assignments/` | `type: assignment`, `module` |

Exceptions: `04-questions/weak-spots.md` is `type: log`; `00-home.md` is `type: home`; `README.md` and `CLAUDE.md` have no frontmatter.

Templates for each type are in `99-meta/templates/`.

## Naming

- `m0N-` prefix only for module-bound files: syntheses, assignments, module hubs (e.g. `m02-internet.md`, `m01-why-binary.md`).
- Concepts and sources keep plain names (`Cache.md`, `crash-course-cs-ep07.md`). Concept filenames are link targets — never rename them casually.
- Question files: `q-<short-kebab-slug>.md`.

## Weekly audit checklist

Report findings; don't fix wording.

1. Unresolved wikilinks: every `[[target]]` / `[[target|alias]]` (ignore `#heading`) with no matching `.md`, `.canvas`, `.base` or media file.
2. Files outside `_archive/` and `99-meta/` (except `README.md`, `CLAUDE.md`) missing `type` or `module`.
3. Questions with `status: open` and `asked` older than 14 days (or `asked` still a TODO).
4. Question files whose "What I now understand" section is empty or still a `TODO (Nasif)`.
5. Concept count per module vs the cap (Module 2 cap: 7).

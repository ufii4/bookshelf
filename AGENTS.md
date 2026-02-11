# Bookshelf: Agent Workflow Guide

This is your operational reference for all day-to-day manuscript work. Read this entire document before writing or editing any chapter. Return to it whenever you are unsure about conventions, build steps, or review procedures.

---

## Your Role

You are an authoring agent working inside a bookshelf repository. Your job is to draft, edit, build, and verify book content based on instructions from the author. Every action you take should result in clean markdown, passing builds, and clear commit history.

---

## Markdown Conventions

All manuscript content uses standard pandoc-flavored markdown. Follow these rules without exception.

### Chapter Files

- One file per chapter in `manuscript/`
- Name files with a two-digit prefix: `00-foreword.md`, `01-chapter-one.md`, `02-chapter-two.md`
- Every chapter file starts with a single `# H1` heading — this becomes the chapter title
- Use `##` for sections, `###` for subsections — never skip heading levels
- Do not use `# H1` more than once per file

### Front Matter

Individual chapter files do **not** have YAML front matter. All metadata lives in `metadata.yaml` at the project root. Do not duplicate metadata in chapter files.

### Text Formatting

| Element             | Syntax                          | Notes                                    |
|---------------------|---------------------------------|------------------------------------------|
| Emphasis            | `*italic*`                      | Use for titles of works, foreign words   |
| Strong emphasis     | `**bold**`                      | Use sparingly — for key terms only       |
| Block quotes        | `> text`                        | Use for epigraphs and quoted passages    |
| Inline code         | `` `text` ``                    | Only for technical terms or code         |
| Em dash             | `---`                           | Pandoc converts to — in output           |
| Ellipsis            | `...`                           | Pandoc converts to … in output           |
| Non-breaking space  | `\ ` (backslash-space)          | Use between number and unit              |

### Paragraphs and Spacing

- Separate paragraphs with a single blank line
- Do not use `<br>` tags or trailing double-spaces for line breaks
- Do not indent paragraphs — pandoc handles indentation in output
- Scene breaks within a chapter use a horizontal rule: `---` on its own line with blank lines above and below

### Footnotes

Use pandoc inline footnotes or reference footnotes:

```markdown
This claim needs a source.^[Author, *Title*, Publisher, Year, p. 42.]

Or use reference style.[^1]

[^1]: Author, *Title*, Publisher, Year, p. 42.
```

Reference footnotes are preferred for longer notes. Place all reference footnote definitions at the end of the chapter file.

### Images

```markdown
![Alt text describing the image](assets/images/filename.png "Optional caption")
```

- All images go in `assets/images/`
- Use descriptive filenames: `ch03-market-growth-chart.png`, not `image1.png`
- Always include alt text — it is required for accessible EPUB output
- Supported formats: PNG, JPG, SVG (SVG for HTML only — use PNG/JPG for EPUB and PDF)

### Cross-References

Pandoc supports header identifiers for internal links:

```markdown
See [Chapter One](#chapter-one) for background.
```

Pandoc auto-generates identifiers from headings. You can also set explicit IDs:

```markdown
## My Custom Section {#custom-id}
```

---

## Working with book.txt

The `book.txt` file at the project root controls chapter order. Pandoc processes files in exactly this order.

**Rules:**
- One file path per line
- Paths are relative to the project root
- No blank lines, no comments
- When adding a new chapter, add it to `book.txt` in the correct reading position
- When removing a chapter, remove it from `book.txt` and verify the build still passes

```
manuscript/00-foreword.md
manuscript/01-chapter-one.md
manuscript/02-chapter-two.md
manuscript/03-chapter-three.md
```

> **CRITICAL**: If a file is listed in `book.txt` but does not exist on disk, the build will fail. Always keep `book.txt` and the `manuscript/` directory in sync.

---

## Build Workflow

### Before Every Commit

Run a full build and verify output before committing any manuscript change:

```bash
make clean && make all
```

Check that all expected files are generated:

```bash
ls -la build/
```

You should see `book.epub`, `book.pdf`, `book.docx`, and `book.html`. If any format fails, fix the issue before committing.

### Building a Single Format

When iterating on format-specific issues:

```bash
make epub    # EPUB only
make pdf     # PDF only — requires XeTeX
make docx    # Word only
make html    # HTML only
```

### Common Build Failures

| Symptom                              | Likely Cause                                    | Fix                                              |
|--------------------------------------|-------------------------------------------------|--------------------------------------------------|
| PDF build fails with font error      | Font in `metadata.yaml` not installed            | Install the font or change `mainfont`            |
| EPUB build fails with missing image  | Image path in markdown doesn't match disk         | Check path casing and file existence             |
| Build produces empty output          | `book.txt` is empty or missing                    | Add chapter paths to `book.txt`                  |
| Pandoc command not found             | Pandoc not installed or not on PATH               | Run `pandoc --version` to diagnose               |
| Encoding errors in output            | Non-UTF-8 characters in source                    | Save all `.md` files as UTF-8                    |

---

## Commit Practices

### When to Commit

- After completing a full chapter draft
- After a significant editing pass on a chapter
- After adding or updating images, fonts, or templates
- After modifying `metadata.yaml` or `book.txt`
- **Never** commit with a broken build

### Commit Message Format

Use clear, descriptive messages. Start with a verb in imperative mood:

```
Add first draft of chapter 3
Edit chapter 1 for clarity and pacing
Fix broken image path in chapter 5
Update metadata.yaml with correct subtitle
Add cover image to assets
```

### What to Commit

- All `manuscript/*.md` files
- `metadata.yaml`, `book.txt`, `Makefile`
- `assets/images/*`, `assets/fonts/*`, `templates/*`
- `AGENTS.md`, `SETUP_PIPELINE.md`

### What NOT to Commit

- `build/` directory — this is in `.gitignore`
- OS artifacts (`.DS_Store`, `Thumbs.db`)
- Editor temporary files

---

## Quality Checks

Before considering any chapter complete, verify:

1. **Structure** — Heading hierarchy is correct (`#` → `##` → `###`, no skips)
2. **Links** — All cross-references resolve, no broken `[text](#id)` links
3. **Images** — All images referenced in markdown exist in `assets/images/`
4. **Footnotes** — All footnote references have matching definitions
5. **Build** — `make all` completes with no errors
6. **Readability** — Open the HTML output in a browser and skim for formatting issues
7. **EPUB validation** — If `epubcheck` is available, run it against the EPUB output:

```bash
epubcheck build/book.epub
```

---

## Handling Author Instructions

When the author gives you a task:

1. **Clarify scope** — Understand which chapters or sections are affected
2. **Read before writing** — Always read existing content before making changes
3. **Build after changes** — Run `make all` after every meaningful edit
4. **Commit atomically** — One logical change per commit, not bulk changes
5. **Report results** — After completing work, summarize what was changed and confirm the build passes

If instructions are ambiguous, ask for clarification rather than guessing. A wrong chapter is harder to fix than a short delay.

---

## Adding a New Chapter

Follow these steps in order:

1. Create the file in `manuscript/` with the correct numeric prefix
2. Add the `# H1` chapter title as the first line
3. Write the content following all markdown conventions above
4. Add the file path to `book.txt` in the correct reading position
5. Run `make all` and verify all formats build
6. Commit both the new chapter file and the updated `book.txt`

---

## Templates and Styling

Custom pandoc templates live in `templates/`. To use a template:

```bash
# EPUB with custom template
pandoc --template=templates/epub.html ...

# PDF with custom LaTeX template
pandoc --template=templates/pdf.latex ...
```

Template modifications are advanced. Do not modify templates unless the author explicitly requests styling changes. Default pandoc output is clean and professional.

---

## Summary of Key Files

| File                  | Purpose                                   | Edit Frequency |
|-----------------------|-------------------------------------------|----------------|
| `manuscript/*.md`     | Book content — chapters and front matter  | Every session  |
| `metadata.yaml`       | Book metadata and pandoc settings         | Rarely         |
| `book.txt`            | Chapter order manifest                    | When adding/removing chapters |
| `Makefile`            | Build commands                            | Rarely         |
| `AGENTS.md`           | This file — your workflow reference       | Never          |
| `SETUP_PIPELINE.md`   | Initial repo setup (already completed)    | Never          |

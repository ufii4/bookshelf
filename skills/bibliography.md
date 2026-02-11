# Skill: Bibliography & Citations

This skill defines how to manage references, citations, and bibliographies for non-fiction manuscripts using pandoc's citation processing.

---

## Overview

Pandoc uses CSL (Citation Style Language) and a `.bib` bibliography file to process citations. You write `[@key]` in the markdown, and pandoc replaces it with a formatted citation and generates a bibliography at the end of the document.

---

## Setup

### Install the Citation Filter

Pandoc 2.19+ includes built-in citation processing via `--citeproc`. No additional installation is needed.

Verify:

```bash
pandoc --version | grep citeproc
```

If `citeproc` is not listed, install `pandoc-citeproc` as a separate filter:

```bash
# Ubuntu/Debian
sudo apt-get install -y pandoc-citeproc

# macOS
brew install pandoc-citeproc
```

### Create the Bibliography File

Create `references.bib` at the project root using BibTeX format:

```bibtex
@book{kahneman2011,
  author    = {Daniel Kahneman},
  title     = {Thinking, Fast and Slow},
  publisher = {Farrar, Straus and Giroux},
  year      = {2011},
  address   = {New York}
}

@article{smith2020,
  author  = {Jane Smith and Robert Lee},
  title   = {The Effects of Cognitive Load on Decision Making},
  journal = {Journal of Behavioral Science},
  year    = {2020},
  volume  = {45},
  number  = {3},
  pages   = {112--134}
}

@online{who2024,
  author  = {{World Health Organization}},
  title   = {Global Health Statistics 2024},
  year    = {2024},
  url     = {https://www.who.int/data/statistics},
  urldate = {2024-06-15}
}
```

### Update metadata.yaml

Add bibliography and citation style settings:

```yaml
bibliography: references.bib
csl: https://www.zotero.org/styles/chicago-author-date
link-citations: true
```

Common CSL styles:

| Style                  | CSL URL                                                  | Use Case              |
|------------------------|----------------------------------------------------------|-----------------------|
| Chicago Author-Date    | `https://www.zotero.org/styles/chicago-author-date`      | General non-fiction    |
| Chicago Notes          | `https://www.zotero.org/styles/chicago-note-bibliography`| History, humanities   |
| APA 7th Edition        | `https://www.zotero.org/styles/apa`                      | Social sciences       |
| MLA 9th Edition        | `https://www.zotero.org/styles/modern-language-association` | Literature, arts   |
| IEEE                   | `https://www.zotero.org/styles/ieee`                     | Technical             |

For offline use, download the `.csl` file and reference the local path instead.

---

## Writing Citations in Markdown

### Inline Citations

```markdown
Studies show that cognitive biases affect decision making [@kahneman2011].

As Smith and Lee [-@smith2020] demonstrated, cognitive load is a factor.

Recent WHO data [@who2024, p. 42] supports this conclusion.
```

| Syntax                        | Output (Chicago Author-Date)             |
|-------------------------------|------------------------------------------|
| `[@kahneman2011]`            | (Kahneman 2011)                          |
| `[-@smith2020]`              | (2020) — suppress author name            |
| `[@kahneman2011, p. 45]`    | (Kahneman 2011, 45)                      |
| `[@kahneman2011; @smith2020]`| (Kahneman 2011; Smith and Lee 2020)      |
| `@kahneman2011`              | Kahneman (2011) — narrative citation     |

### Adding the Bibliography

Pandoc automatically appends the bibliography at the end of the document. To control where it appears, add a heading at the end of your last chapter or in a dedicated back-matter file:

```markdown
# Bibliography
```

Or use a div:

```markdown
::: {#refs}
:::
```

This div tells pandoc exactly where to insert the reference list.

---

## Building with Citations

Add `--citeproc` to the pandoc command. Update the `Makefile` targets:

```makefile
PANDOC_FLAGS = --metadata-file=$(METADATA) --toc --toc-depth=2 --number-sections --citeproc
```

That single flag addition enables citations across all output formats. No other changes are needed.

---

## Managing the .bib File

### Adding New References

When the author introduces a new citation:

1. Add the BibTeX entry to `references.bib`
2. Use a consistent key format: `authorYYYY` (e.g., `kahneman2011`, `smith2020`)
3. For multiple works by the same author in the same year, append a letter: `smith2020a`, `smith2020b`
4. Verify the entry parses correctly by building: `make epub`

### Entry Types

| Type          | Use For                                    | Required Fields                              |
|---------------|--------------------------------------------|----------------------------------------------|
| `@book`       | Books                                      | author, title, publisher, year               |
| `@article`    | Journal articles                           | author, title, journal, year                 |
| `@incollection`| Chapter in an edited volume               | author, title, booktitle, publisher, year    |
| `@online`     | Web pages and online resources             | author, title, year, url                     |
| `@thesis`     | Dissertations and theses                   | author, title, school, year                  |
| `@report`     | Technical reports                          | author, title, institution, year             |

### Validating References

Check for unused or missing references:

```bash
# Find all citation keys used in the manuscript
grep -roh '@[a-zA-Z0-9_-]*' manuscript/ | sort -u

# List all keys defined in references.bib
grep -oP '(?<=@\w{1,20}\{)[a-zA-Z0-9_-]+' references.bib | sort -u
```

Compare the two lists. Every key used in the manuscript must have a matching entry in `references.bib`. Unused entries are fine — they are simply not printed.

---

## Rules

- **Every claim needs a citation** — if the author states a fact, statistic, or references someone's work, it must have a `[@key]`
- **Verify before building** — a missing `.bib` entry causes pandoc to print the raw key instead of a formatted citation, which is easy to miss
- **One .bib file** — keep all references in a single `references.bib` at the project root
- **Consistent key format** — `authorYYYY` lowercase, no spaces
- **Commit the .bib file** with every change — it is part of the manuscript source
- **Do not edit the generated bibliography** — it is produced automatically from the `.bib` file and CSL style

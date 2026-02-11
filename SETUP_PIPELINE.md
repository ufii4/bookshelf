# Bookshelf: Pipeline Setup Guide

This document is for **blank agents** initializing a new book project repository. Follow every section in order. Do not skip steps — the pipeline will not function correctly with missing dependencies or incorrect structure.

---

## What This Pipeline Does

Bookshelf is a markdown-based book authoring pipeline. Authors write in plain markdown. The pipeline converts that source into publish-ready formats using **pandoc**:

- **EPUB** — for e-readers and digital distribution
- **PDF** — for print and formal distribution
- **DOCX (Word)** — for editorial review and traditional publishing workflows
- **HTML** — for web previews and online reading

All book content lives as `.md` files. Formatting, metadata, and structure are controlled through markdown and YAML front matter. There is no GUI, no proprietary format, and no lock-in.

---

## Step 1: Install Required Tools

These tools **must** be installed before the pipeline can produce any output. Run all commands in the project root.

### Pandoc (Required)

Pandoc is the core conversion engine. Every output format depends on it.

```bash
# Ubuntu/Debian
sudo apt-get update && sudo apt-get install -y pandoc

# macOS
brew install pandoc

# Verify installation
pandoc --version
```

> **CRITICAL**: The minimum required version is **pandoc 2.19+**. Older versions lack EPUB3 support and have markdown parsing inconsistencies. Always verify the version after install.

### LaTeX / XeTeX (Required for PDF)

PDF output requires a LaTeX engine. XeTeX is preferred because it handles Unicode and custom fonts without extra configuration.

```bash
# Ubuntu/Debian — install the full texlive-xetex package
sudo apt-get install -y texlive-xetex texlive-fonts-recommended texlive-fonts-extra texlive-latex-extra

# macOS
brew install --cask mactex-no-gui
```

```bash
# Verify installation
xelatex --version
```

> If you only need EPUB and DOCX output, you may skip LaTeX. But most book projects eventually need PDF — install it now to avoid pipeline failures later.

### Make (Recommended)

A `Makefile` drives the build commands. Most systems have `make` pre-installed.

```bash
# Verify it exists
make --version

# If missing on Ubuntu/Debian
sudo apt-get install -y build-essential
```

### Calibre (Optional — for Kindle/MOBI)

Only needed if the project targets Amazon Kindle distribution.

```bash
# Ubuntu/Debian
sudo apt-get install -y calibre

# macOS
brew install --cask calibre

# The key binary is ebook-convert
ebook-convert --version
```

---

## Step 2: Initialize the Repository Structure

After tools are installed, create the standard project layout. **Every bookshelf project must follow this structure** so that build scripts, templates, and the agent workflow operate correctly.

```bash
# Create the directory skeleton
mkdir -p manuscript
mkdir -p templates
mkdir -p build
mkdir -p assets/images
mkdir -p assets/fonts
```

### Directory Purposes

| Directory         | Purpose                                                       |
|-------------------|---------------------------------------------------------------|
| `manuscript/`     | All book content as `.md` files, one per chapter              |
| `templates/`      | Pandoc templates for EPUB, PDF, and DOCX styling              |
| `build/`          | Output directory for generated files — **never commit these** |
| `assets/images/`  | Cover art, figures, diagrams referenced in the manuscript     |
| `assets/fonts/`   | Custom fonts for PDF and EPUB rendering                       |

### Create the Book Metadata File

Every project needs a `metadata.yaml` at the root. This file controls title, author, language, and pandoc conversion settings.

```bash
cat > metadata.yaml << 'EOF'
---
title: "Book Title"
subtitle: "Subtitle"
author: "Author Name"
date: "2026"
lang: en-US
rights: "All rights reserved"

# EPUB settings
epub-cover-image: assets/images/cover.png
epub-metadata: null

# PDF settings (XeTeX)
documentclass: book
geometry: "margin=1in"
fontsize: 12pt
mainfont: "DejaVu Serif"
monofont: "DejaVu Sans Mono"
linestretch: 1.25

# Shared settings
toc: true
toc-depth: 2
number-sections: true
---
EOF
```

> Modify the values above to match the actual book. The `mainfont` and `monofont` must be fonts available on the system or placed in `assets/fonts/`.

### Create the Chapter Order File

Pandoc processes files in the order they are given. Create a `book.txt` manifest that lists chapters in reading order:

```
manuscript/00-foreword.md
manuscript/01-chapter-one.md
manuscript/02-chapter-two.md
manuscript/03-chapter-three.md
```

Each line is a path to a chapter file, processed sequentially.

---

## Step 3: Set Up the Makefile

The `Makefile` provides repeatable build commands. Create it at the project root.

```makefile
METADATA = metadata.yaml
CHAPTERS = $(shell cat book.txt)
OUTPUT_DIR = build

EPUB_OUT = $(OUTPUT_DIR)/book.epub
PDF_OUT  = $(OUTPUT_DIR)/book.pdf
DOCX_OUT = $(OUTPUT_DIR)/book.docx
HTML_OUT = $(OUTPUT_DIR)/book.html

PANDOC_FLAGS = --metadata-file=$(METADATA) --toc --toc-depth=2 --number-sections

.PHONY: all epub pdf docx html clean

all: epub pdf docx html

epub: $(EPUB_OUT)
pdf: $(PDF_OUT)
docx: $(DOCX_OUT)
html: $(HTML_OUT)

$(EPUB_OUT): $(CHAPTERS) $(METADATA) book.txt
	@mkdir -p $(OUTPUT_DIR)
	pandoc $(PANDOC_FLAGS) -o $@ $(CHAPTERS)

$(PDF_OUT): $(CHAPTERS) $(METADATA) book.txt
	@mkdir -p $(OUTPUT_DIR)
	pandoc $(PANDOC_FLAGS) --pdf-engine=xelatex -o $@ $(CHAPTERS)

$(DOCX_OUT): $(CHAPTERS) $(METADATA) book.txt
	@mkdir -p $(OUTPUT_DIR)
	pandoc $(PANDOC_FLAGS) -o $@ $(CHAPTERS)

$(HTML_OUT): $(CHAPTERS) $(METADATA) book.txt
	@mkdir -p $(OUTPUT_DIR)
	pandoc $(PANDOC_FLAGS) --standalone -o $@ $(CHAPTERS)

clean:
	rm -rf $(OUTPUT_DIR)/*
```

### Build Commands

```bash
# Build all formats
make all

# Build a single format
make epub
make pdf
make docx
make html

# Clean generated files
make clean
```

---

## Step 4: Create the .gitignore

The `build/` directory contains generated artifacts and must not be committed.

```
# Generated output
build/

# OS files
.DS_Store
Thumbs.db

# Editor swap files
*.swp
*.swo
*~
```

---

## Step 5: Download the Agent Instructions

Once the repository structure is in place, download the day-to-day agent workflow guide. This file contains instructions for writing, editing, building, and managing the manuscript during active work sessions.

```bash
# Download the agent workflow instructions
curl -o AGENT.md https://raw.githubusercontent.com/bookshelf-pipeline/bookshelf/main/AGENT.md
```

> **PLACEHOLDER**: The URL above is a placeholder. Replace it with the actual hosted location of `AGENT.md` once available. The `AGENT.md` file contains all operational instructions for agents working on the manuscript day-to-day — chapter drafting conventions, commit practices, build verification steps, and quality checks.

After downloading, **read `AGENT.md` in full** before beginning any manuscript work. It is your primary reference for all ongoing tasks.

---

## Step 6: Verify the Pipeline

Before starting any writing, confirm the pipeline works end-to-end.

1. Create a minimal test chapter:

```bash
cat > manuscript/01-chapter-one.md << 'EOF'
# Chapter One

This is a test chapter to verify the pipeline.

## Section 1.1

Content goes here. The pipeline converts this markdown into multiple output formats.
EOF
```

2. Create the chapter manifest:

```bash
echo "manuscript/01-chapter-one.md" > book.txt
```

3. Run the build:

```bash
make all
```

4. Verify output files exist:

```bash
ls -la build/
# Expected: book.epub, book.pdf, book.docx, book.html
```

> If any format fails, check the error output carefully. The most common issues are:
> - **PDF fails**: LaTeX/XeTeX not installed or font not found
> - **EPUB fails**: Pandoc version too old or malformed metadata.yaml
> - **All fail**: Pandoc not installed or not on PATH

---

## Quick Reference

| Task                     | Command             |
|--------------------------|---------------------|
| Build all formats        | `make all`          |
| Build EPUB only          | `make epub`         |
| Build PDF only           | `make pdf`          |
| Build Word only          | `make docx`         |
| Build HTML only          | `make html`         |
| Clean build directory    | `make clean`        |
| Verify pandoc version    | `pandoc --version`  |
| Verify LaTeX             | `xelatex --version` |

---

## What Comes Next

After setup is complete and verified, all ongoing work follows the instructions in **[AGENT.md](AGENT.md)**. That document covers:

- Chapter drafting and markdown conventions
- Commit message standards and branching
- Build verification before every push
- Review workflows and quality checks
- Handling images, footnotes, and cross-references

**Do not begin manuscript work until you have read AGENT.md.**

# Bookshelf

A markdown-based book authoring pipeline designed for AI agents. Authors write in plain markdown, and the pipeline produces publish-ready EPUB, PDF, Word, and HTML using pandoc.

## How It Works

1. An author gives their AI agent a single prompt pointing to `SETUP_PIPELINE.md`
2. The agent reads the setup instructions, installs dependencies (pandoc, XeTeX, make), and scaffolds the project
3. The agent downloads `AGENTS.md` into the project — this becomes its operational guide for all ongoing work
4. The author writes. The agent drafts, edits, builds, and manages version control

No manual configuration. No proprietary formats. Everything is markdown and git.

## What's in This Repo

| File / Directory        | Purpose                                                    |
|-------------------------|------------------------------------------------------------|
| `SETUP_PIPELINE.md`    | One-time setup instructions for agents initializing a repo |
| `AGENTS.md`            | Day-to-day workflow guide for agents working on manuscripts |
| `skills/`              | Optional skill files agents can download on demand          |

### Optional Skills

| Skill                     | File                        | Description                                        |
|---------------------------|-----------------------------|----------------------------------------------------|
| Proofreading & Editing    | `skills/proofreading.md`    | Structured multi-pass review — grammar, consistency, style |
| Word Count & Progress     | `skills/word-count.md`      | Per-chapter word counts, progress tracking, outlier detection |
| Search & Replace          | `skills/search-replace.md`  | Safe bulk text changes across a manuscript          |
| Bibliography & Citations  | `skills/bibliography.md`    | BibTeX references and pandoc-citeproc for non-fiction |
| Translation               | Built into `AGENTS.md`      | Translate the full manuscript into another language  |

## Quick Start

Give your AI agent this prompt:

```
Follow the instructions at https://raw.githubusercontent.com/ufii4/bookshelf/master/SETUP_PIPELINE.md
```

The agent handles everything from there.

## Pipeline Output

From a single markdown source, the pipeline produces:

- **EPUB** — e-readers and digital distribution
- **PDF** — print-ready via XeTeX
- **DOCX** — editorial review and traditional publishing
- **HTML** — web preview and online reading

## Contributing

Pull requests are welcome. Areas where contributions would be especially useful:

- **New skills** — additional skill files for capabilities like audiobook script preparation, index generation, or series management
- **Templates** — pandoc templates for specific publishers, styles, or genres
- **Internationalization** — improvements to the translation workflow or language-specific build configurations
- **Documentation** — clearer instructions, edge case coverage, or workflow improvements

To contribute:

1. Fork this repository
2. Create a branch (`git checkout -b feature/your-feature`)
3. Make your changes
4. Submit a pull request

Please keep skill files self-contained — each skill should work independently without modifying the core `AGENTS.md` or `SETUP_PIPELINE.md`.

## License

This project is open source. See [LICENSE](LICENSE) for details.

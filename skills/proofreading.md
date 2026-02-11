# Skill: Proofreading & Editing

This skill defines how to perform structured review passes on the manuscript. Each pass focuses on a single concern. Do not combine passes — fixing grammar while checking plot consistency leads to missed errors in both.

---

## Review Passes

Run these passes in order. Complete one fully before starting the next.

### Pass 1: Spelling & Grammar

Read each chapter and fix:

- Misspelled words
- Subject-verb agreement errors
- Incorrect punctuation (misplaced commas, missing periods, wrong quote marks)
- Run-on sentences and sentence fragments
- Incorrect homophones (their/there/they're, its/it's, affect/effect)

Do not change the author's voice or rewrite sentences for style during this pass. Only fix objective errors.

### Pass 2: Consistency Audit

Check for consistency across the entire manuscript:

- **Character names** — verify spelling is identical everywhere (e.g., "Catherine" is never "Catharine")
- **Place names** — same spelling, same capitalization throughout
- **Terminology** — if a concept is introduced as "neural link" in chapter 2, it should not become "neuro-link" in chapter 7
- **Timeline** — events referenced in later chapters must match when they were described earlier
- **Numbers and units** — consistent formatting (e.g., always "10 kilometers" or always "ten kilometers", not mixed)
- **Honorifics and titles** — "Dr. Miller" should not become "Doctor Miller" unless intentional

Create a tracking list as you go:

```markdown
## Consistency Log

| Term / Name       | Canonical Form       | Variations Found          | Chapters Affected |
|--------------------|----------------------|---------------------------|-------------------|
| Catherine          | Catherine             | Catharine (ch. 5, 9)     | 5, 9              |
| neural link        | neural link           | neuro-link (ch. 7)       | 7                 |
```

Fix all variations to match the canonical form. If unsure which form the author prefers, ask before changing.

### Pass 3: Style & Tone

Review each chapter for:

- **Passive voice overuse** — flag sentences that would be stronger in active voice, but do not eliminate all passive voice (some is natural)
- **Repetition** — same word or phrase used multiple times in close proximity
- **Pacing** — paragraphs that are too long or too short relative to the scene's energy
- **Dialogue tags** — overuse of adverb-heavy tags ("she said angrily") vs. action beats
- **Show vs. tell** — flag passages that tell the reader what to feel instead of showing through action and detail

> **IMPORTANT**: Style changes are subjective. When this pass produces changes, present them to the author as suggestions rather than applying them silently. List the original and proposed revision side by side.

### Pass 4: Final Read-Through

Read the manuscript start to finish as a reader would. Flag anything that:

- Breaks immersion
- Feels confusing or unclear
- Contradicts something established earlier
- Feels rushed or dragged out

Report findings as a numbered list with chapter and approximate location, not as direct edits.

---

## Workflow

1. Create a branch for the review:

```bash
git checkout -b edit/proofread
```

2. Run passes 1 and 2, committing fixes per chapter:

```bash
git add manuscript/03-chapter-three.md
git commit -m "Proofread chapter 3: fix spelling and consistency"
```

3. For pass 3, write suggestions into a `review-notes.md` file instead of editing the manuscript directly:

```bash
# Create review notes
touch review-notes.md
```

4. For pass 4, append final read-through observations to `review-notes.md`.

5. Present `review-notes.md` to the author. Apply only the changes they approve.

6. After all approved changes are applied and the build passes:

```bash
make clean && make all
git checkout main
git merge edit/proofread
git branch -d edit/proofread
```

---

## Rules

- **Never rewrite the author's voice** — fix errors, do not impose a different style
- **One pass at a time** — complete each pass fully before starting the next
- **Objective fixes are applied directly** — spelling, grammar, consistency
- **Subjective changes are proposed** — style, tone, pacing go into `review-notes.md`
- **Always verify the build** after making changes
- **Commit per chapter** so changes are traceable

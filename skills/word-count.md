# Skill: Word Count & Progress Tracking

This skill defines how to measure manuscript length, track progress toward targets, and report on chapter-level statistics.

---

## Counting Words

Use `wc -w` to count words in manuscript files. Pandoc markdown syntax (headings, emphasis markers, link syntax) is included in the count — this is standard for manuscript word counts.

### Total Manuscript Word Count

```bash
cat $(cat book.txt) | wc -w
```

### Per-Chapter Word Count

```bash
for f in $(cat book.txt); do
  words=$(wc -w < "$f")
  printf "%-45s %6d\n" "$f" "$words"
done
```

### Word Count Summary with Average

```bash
total=0
count=0
for f in $(cat book.txt); do
  words=$(wc -w < "$f")
  printf "%-45s %6d\n" "$f" "$words"
  total=$((total + words))
  count=$((count + 1))
done
echo "---"
printf "%-45s %6d\n" "TOTAL" "$total"
if [ "$count" -gt 0 ]; then
  printf "%-45s %6d\n" "AVERAGE" "$((total / count))"
fi
```

---

## Progress Tracking

When the author sets a word count target (e.g., "80,000 word novel"), track progress against it.

### Reporting Progress

Report in this format:

```
## Manuscript Progress

Target:     80,000 words
Current:    52,340 words
Remaining:  27,660 words
Progress:   65.4%

### Per-Chapter Breakdown

| Chapter                              | Words  | % of Total |
|--------------------------------------|--------|------------|
| manuscript/01-chapter-one.md         |  4,210 |       8.0% |
| manuscript/02-chapter-two.md         |  5,830 |      11.1% |
| manuscript/03-chapter-three.md       |  3,920 |       7.5% |
| ...                                  |    ... |        ... |
```

### Flagging Outliers

Flag chapters that are significantly shorter or longer than the average:

- **Short** — less than 50% of the average chapter length
- **Long** — more than 200% of the average chapter length

These are not errors — some chapters are naturally shorter or longer. Report them so the author can decide whether to adjust.

```
### Outlier Chapters

- manuscript/07-chapter-seven.md: 1,200 words (average is 4,500 — significantly short)
- manuscript/12-chapter-twelve.md: 11,300 words (average is 4,500 — significantly long)
```

---

## Tracking Translations

For translated manuscripts, report word counts separately:

```bash
# Word count for a specific translation
cat $(cat translations/es/book.txt) | wc -w
```

Note that word counts will differ between languages. Spanish and French typically run 15–25% longer than English. Japanese and Chinese may have lower word counts but similar page counts. Do not flag translation word count differences as problems.

---

## When to Report

Provide a word count report:

- When the author asks for a progress update
- After completing a new chapter draft
- After a major editing pass (word counts may change significantly)
- At the start of a new work session, to orient yourself

Do not include word count reports in commits. They are ephemeral — generate them on demand.

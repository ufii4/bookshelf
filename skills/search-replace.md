# Skill: Search & Replace

This skill defines how to perform bulk text changes across the entire manuscript safely and traceably.

---

## When to Use

- Renaming a character (e.g., "James" → "Marcus")
- Changing a place name (e.g., "Millbrook" → "Thornfield")
- Standardizing a term (e.g., "e-mail" → "email" everywhere)
- Fixing a repeated misspelling across multiple chapters
- Updating a title or honorific (e.g., "Mrs." → "Ms.")

---

## Procedure

### Step 1: Audit Before Changing

Before making any replacements, find every occurrence and review the context:

```bash
grep -rn "James" manuscript/
```

Review the output carefully. Not every match should be replaced:

- "James" the character should become "Marcus"
- "James River" is a real place name and should **not** change
- "Jameson" is a different character and should **not** change

Build an explicit list of what changes and what stays.

### Step 2: Create a Branch

```bash
git checkout -b feature/rename-james-to-marcus
```

### Step 3: Perform Replacements

Use `sed` for straightforward replacements. Always use word boundaries to avoid partial matches:

```bash
# Preview changes first (does not modify files)
for f in $(cat book.txt); do
  sed -n 's/\bJames\b/Marcus/gp' "$f"
done

# Apply changes
for f in $(cat book.txt); do
  sed -i 's/\bJames\b/Marcus/gp' "$f"
done
```

For changes that require context-aware judgment (e.g., "James" the character vs. "James River"), edit each file manually instead of using `sed`.

### Step 4: Verify Results

After replacing, audit again to confirm:

```bash
# Confirm no old term remains where it shouldn't
grep -rn "James" manuscript/

# Confirm new term appears where expected
grep -rn "Marcus" manuscript/
```

### Step 5: Verify the Build

```bash
make clean && make all
```

### Step 6: Commit and Merge

```bash
git add manuscript/
git commit -m "Rename character James to Marcus across all chapters"
git checkout main
git merge feature/rename-james-to-marcus
git branch -d feature/rename-james-to-marcus
```

---

## Handling Translations

If the manuscript has translations, the same change likely applies there too — but translated names may differ. Coordinate with the author:

- Some names are kept in the original language across translations
- Some names are localized (e.g., "James" → "Santiago" in Spanish)
- Place names may or may not be translated

**Never apply a search-and-replace to translations without explicit author approval.** Ask first.

---

## Rules

- **Always audit first** — `grep` before `sed`, every time
- **Use word boundaries** — `\bterm\b` prevents partial matches
- **Preview before applying** — run `sed` without `-i` first to see what would change
- **One logical change per branch** — do not combine a character rename with a terminology fix
- **Check for possessives and plurals** — "James's" needs to become "Marcus's", "Jameses" is a different case
- **Verify the build** after every replacement
- **Manual over automated when in doubt** — if the replacement has exceptions, edit by hand rather than risking a bad `sed`

---

## Common Patterns

| Change Type              | Command Pattern                                      |
|--------------------------|------------------------------------------------------|
| Simple word replacement  | `sed -i 's/\bOldTerm\b/NewTerm/g' file.md`          |
| Case-sensitive           | `sed -i 's/\bOldTerm\b/NewTerm/g' file.md` (default)|
| Case-insensitive         | `sed -i 's/\boldterm\b/NewTerm/gI' file.md`         |
| With possessive          | `sed -i "s/\bJames's\b/Marcus's/g" file.md`         |
| Multiple files           | `for f in $(cat book.txt); do sed -i '...' "$f"; done` |

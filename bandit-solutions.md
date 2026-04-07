# OverTheWire Bandit — Solutions Log

Documenting every level with the Linux concept it teaches.
No passwords stored here — commands only.

---

## Level 0 → 1
**Concept:** Reading a file
**Command:** cat readme

## Level 1 → 2
**Concept:** Files with special character names (dash)
**Command:** cat ./-
**Why:** Single dash means stdin in Linux. ./ forces file path context.

## Level 2 → 3
**Concept:** Files with spaces in the name
**Command:** cat "spaces in this filename."
**Why:** Quotes prevent the shell from splitting on spaces.

## Level 3 → 4
**Concept:** Hidden files (dot files)
**Command:** cd inhere → ls -la → cat .hidden
**Why:** ls alone hides dotfiles. ls -la shows everything.

---
*Updated daily as I progress*

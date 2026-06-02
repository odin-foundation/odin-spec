# %levenshtein

edit distance between two strings

**Signature:** `%levenshtein <string> <string> -> integer`

Return the minimum single-character edits to turn one string into another.

**Transform**

```odin
{out}
out = %levenshtein @.a @.b
```

**In**

```odin
a = "kitten"
b = "sitting"
```

**Out**

```odin
{out}
out = ##3
```

**Notes**

- Two required string arguments. Counts insertions, deletions, and substitutions. Returns an integer (## prefix).
- Inputs longer than an internal length limit return null.

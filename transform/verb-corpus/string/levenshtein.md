# %levenshtein

edit distance between two strings

**Signature:** `%levenshtein <string> <string> -> integer`

Return the minimum single-character edits to turn one string into another.

**Transform**

```odin
{out}
edit = %levenshtein @.a @.b
same = %levenshtein @.same1 @.same2
fromEmpty = %levenshtein @.empty @.word
```

**In**

```odin
a = "kitten"
b = "sitting"
same1 = "abc"
same2 = "abc"
empty = ""
word = "abc"
```

**Out**

```odin
{out}
edit = ##3
same = ##0
fromEmpty = ##3
```

**Notes**

- Two required string arguments. Counts insertions, deletions, and substitutions. Returns an integer (## prefix).

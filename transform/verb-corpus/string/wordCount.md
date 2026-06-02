# %wordCount

count words

**Signature:** `%wordCount <string> -> integer`

Count the whitespace-separated words in a string.

**Transform**

```odin
{out}
out = %wordCount @.text
single = %wordCount @.one
padded = %wordCount @.spaced
empty = %wordCount @.blank
```

**In**

```odin
text = "the quick brown fox"
one = "hello"
spaced = "  a   b  "
blank = ""
```

**Out**

```odin
{out}
out = ##4
single = ##1
padded = ##2
empty = ##0
```

**Notes**

- Single argument. Returns an integer (## prefix). Empty or whitespace-only input yields 0.

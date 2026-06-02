# %wordCount

count words

**Signature:** `%wordCount <string> -> integer`

Count the whitespace-separated words in a string.

**Transform**

```odin
{out}
out = %wordCount @.text
```

**In**

```odin
text = "the quick brown fox"
```

**Out**

```odin
{out}
out = ##4
```

**Notes**

- Single argument. Returns an integer (## prefix). Empty or whitespace-only input yields 0.

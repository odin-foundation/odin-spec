# %wrap

word-wrap to a width

**Signature:** `%wrap <string> <width:integer> -> string`

Insert newlines at word boundaries so each line fits within a width.

**Transform**

```odin
{out}
out = %wrap @.text ##10
```

**In**

```odin
text = "the quick brown fox"
```

**Out**

```odin
{out}
out = "the quick\nbrown fox"
```

**Notes**

- Two required arguments: the string and the line width. Wrapping happens at whitespace; lines are joined with newline characters.
- A width of zero or less returns null; a string already within the width is returned unchanged.

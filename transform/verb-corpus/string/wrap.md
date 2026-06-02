# %wrap

word-wrap to a width

**Signature:** `%wrap <string> <width:integer> -> string`

Insert newlines at word boundaries so each line fits within a width.

**Transform**

```odin
{out}
out = %wrap @.text ##10
short = %wrap @.tiny ##10
exact = %wrap @.exact ##5
zero = %wrap @.text ##0
```

**In**

```odin
text = "the quick brown fox"
tiny = "short"
exact = "12345"
```

**Out**

```odin
{out}
out = "the quick\nbrown fox"
short = "short"
exact = "12345"
zero = ~
```

**Notes**

- Two required arguments: the string and the line width. Wrapping happens at whitespace; lines are joined with newline characters.
- A width of zero or less returns null; a string already within the width is returned unchanged.

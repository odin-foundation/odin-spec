# %truncate

cut a string to a max length

**Signature:** `%truncate <string> <length:integer> -> string`

Return at most the first N characters of a string.

**Transform**

```odin
{out}
out = %truncate @.name ##3
whole = %truncate @.name ##99
exact = %truncate @.name ##5
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = "Hon"
whole = "Honda"
exact = "Honda"
```

**Notes**

- Two required arguments: string and max length.
- No ellipsis is appended - the result is a plain slice of the first N characters.

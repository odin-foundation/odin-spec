# %truncate

cut a string to a max length

**Signature:** `%truncate <string> <length:integer> -> string`

Return at most the first N characters of a string.

**Transform**

```odin
{out}
out = %truncate @.name ##3
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = "Hon"
```

**Notes**

- Two required arguments: string and max length.
- No ellipsis is appended - the result is a plain slice of the first N characters.

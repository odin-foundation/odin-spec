# %substring

extract by start and length

**Signature:** `%substring <string> <start:integer> <length:integer> -> string`

Return the slice of a string starting at an index for a given length.

**Transform**

```odin
{out}
out = %substring @.name ##1 ##3
fromStart = %substring @.name ##0 ##2
overLen = %substring @.name ##2 ##99
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = "ond"
fromStart = "Ho"
overLen = "nda"
```

**Notes**

- Three required arguments: string, zero-based start index, then length (not an end index).
- All three are required - a two-argument call returns null.

**Avoid**

- `out = %substring @.name ##3 ##1`: second arg is start and third is length, not start/end; ##3 ##1 starts at index 3 for length 1 → "d", not "ond"

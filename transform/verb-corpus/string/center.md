# %center

center-pad to a width

**Signature:** `%center <string> <width:integer> [char:string] -> string`

Pad both sides of a string to center it within a target width.

**Transform**

```odin
{out}
out = %center @.hi ##6 "-"
odd = %center @.hi ##7 "-"
wide = %center @.long ##3 "-"
spacePad = %center @.hi ##6 " "
```

**In**

```odin
hi = "hi"
long = "already wide"
```

**Out**

```odin
{out}
out = "--hi--"
odd = "--hi---"
wide = "already wide"
spacePad = "  hi  "
```

**Notes**

- Two required arguments plus an optional fill character (default space). When the padding is odd, the extra character goes on the right.
- A string already at or above the width is returned unchanged.

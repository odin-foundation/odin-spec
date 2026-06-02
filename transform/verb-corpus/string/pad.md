# %pad

pad to a width (right side)

**Signature:** `%pad <string> <length:integer> <char:string> -> string`

Pad the end of a string with a fill character until it reaches a target length.

**Transform**

```odin
{out}
out = %pad @.code ##6 "."
```

**In**

```odin
code = "42"
```

**Out**

```odin
{out}
out = "42...."
```

**Notes**

- Pads on the right, behaving like %padRight.
- Three required arguments: string, target length, fill character; only the first character of the fill is used.

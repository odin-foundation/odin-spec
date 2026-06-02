# %pad

pad to a width (right side)

**Signature:** `%pad <string> <length:integer> <char:string> -> string`

Pad the end of a string with a fill character until it reaches a target length.

**Transform**

```odin
{out}
padded = %pad @.code ##6 "."
already = %pad @.long ##3 "."
exact = %pad @.exact ##2 "."
```

**In**

```odin
code = "42"
long = "abcdef"
exact = "ab"
```

**Out**

```odin
{out}
padded = "42...."
already = "abcdef"
exact = "ab"
```

**Notes**

- Pads on the right, behaving like %padRight.
- Three required arguments: string, target length, fill character; only the first character of the fill is used.

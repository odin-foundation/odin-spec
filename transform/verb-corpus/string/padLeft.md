# %padLeft

left-pad to a width

**Signature:** `%padLeft <string> <length:integer> <char:string> -> string`

Pad the start of a string with a fill character until it reaches a target length.

**Transform**

```odin
{out}
padded = %padLeft @.code ##6 "0"
already = %padLeft @.long ##3 "0"
exact = %padLeft @.exact ##2 "0"
```

**In**

```odin
code = "42"
long = "123456"
exact = "42"
```

**Out**

```odin
{out}
padded = "000042"
already = "123456"
exact = "42"
```

**Notes**

- Three required arguments: string, target length, fill character.
- Only the first character of the fill argument is used. A string already at or above the length is returned unchanged.

**Avoid**

- `out = %padLeft @.code ##6`: omitting the fill-character argument fails the 3-argument requirement and yields null (~), not space-padding

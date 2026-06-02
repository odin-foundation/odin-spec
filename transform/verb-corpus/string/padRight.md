# %padRight

right-pad to a width

**Signature:** `%padRight <string> <length:integer> <char:string> -> string`

Pad the end of a string with a fill character until it reaches a target length.

**Transform**

```odin
{out}
padded = %padRight @.code ##6 "0"
already = %padRight @.long ##3 "0"
exact = %padRight @.exact ##2 "0"
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
padded = "420000"
already = "123456"
exact = "42"
```

**Notes**

- Three required arguments: string, target length, fill character.
- Only the first character of the fill argument is used. A string already at or above the length is returned unchanged.

**Avoid**

- `out = %padRight @.code ##6`: omitting the fill-character argument fails the 3-argument requirement and yields null (~)

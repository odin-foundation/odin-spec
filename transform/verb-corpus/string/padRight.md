# %padRight

right-pad to a width

**Signature:** `%padRight <string> <length:integer> <char:string> -> string`

Pad the end of a string with a fill character until it reaches a target length.

**Transform**

```odin
{out}
out = %padRight @.code ##6 "0"
```

**In**

```odin
code = "42"
```

**Out**

```odin
{out}
out = "420000"
```

**Notes**

- Three required arguments: string, target length, fill character.
- Only the first character of the fill argument is used. A string already at or above the length is returned unchanged.

**Avoid**

- `out = %padRight @.code ##6`: omitting the fill-character argument fails the 3-argument requirement and yields null (~)

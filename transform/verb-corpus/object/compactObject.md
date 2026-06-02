# %compactObject

drop empty entries

**Signature:** `%compactObject <object> -> object`

Return a copy of an object with null, empty-string, empty-array, and empty-object values removed.

**Transform**

```odin
{out}
c = %compactObject @.rec
```

**In**

```odin
{rec}
name = "Ada"
middle = ~
nickname = ""
```

**Out**

```odin
out.c.name = "Ada"
```

**Notes**

- Drops ~ (null), empty strings, empty arrays, and empty objects; other falsy values such as ##0 and ?false are kept.

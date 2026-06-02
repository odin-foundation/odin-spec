# %fromEntries

object from key/value pairs

**Signature:** `%fromEntries <array> -> object`

Build an object from an array of two-element [key, value] pairs.

**Transform**

```odin
{out}
o = %fromEntries @.pairs
nonArray = %fromEntries @.notArr
```

**In**

```odin
notArr = "x"

{pairs[0][] : ~}
"name"
"Ada"

{pairs[1][] : ~}
"role"
"admin"
```

**Out**

```odin
{out}
nonArray = ~
{.o}
name = "Ada"
role = "admin"
```

**Notes**

- The inverse of %entries. Each pair is [key, value]; later pairs win on duplicate keys.

# %merge

shallow merge of two objects

**Signature:** `%merge <object> <object> -> object`

Combine two objects, with the second object's properties overriding the first.

**Transform**

```odin
{out}
m = %merge @.a @.b
nonObject = %merge @.notObj @.b
```

**In**

```odin
notObj = "x"

{a}
name = "Ada"
role = "user"

{b}
role = "admin"
active = ?true
```

**Out**

```odin
{out}
{.m}
name = "Ada"
role = "admin"
active = ?true
{.nonObject}
role = "admin"
active = ?true
```

**Notes**

- Both arguments are objects; the result is a new object serialized as a {.m} sub-section.
- Keys in the second object override matching keys in the first (role becomes "admin"); the originals are untouched.
- The merge is shallow - nested objects are replaced, not deep-merged.

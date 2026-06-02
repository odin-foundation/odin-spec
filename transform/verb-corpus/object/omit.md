# %omit

drop named keys

**Signature:** `%omit <object> <key...> -> object`

Return a copy of an object with the named keys removed.

**Transform**

```odin
{out}
dropped = %omit @.rec "active"
absentKey = %omit @.rec "zzz"
nonObject = %omit @.notObj "name"
```

**In**

```odin
notObj = "x"

{rec}
name = "Ada"
role = "admin"
active = ?true
```

**Out**

```odin
{out}
nonObject = ~
{.dropped}
name = "Ada"
role = "admin"
{.absentKey}
name = "Ada"
role = "admin"
active = ?true
```

**Notes**

- Keys not present in the object are ignored; remaining key order is preserved.

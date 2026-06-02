# %pick

keep only named keys

**Signature:** `%pick <object> <key...> -> object`

Return a copy of an object containing only the named keys.

**Transform**

```odin
{out}
kept = %pick @.rec "name" "role"
absentKey = %pick @.rec "name" "zzz"
nonObject = %pick @.notObj "name"
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
absentKey.name = "Ada"
nonObject = ~
{.kept}
name = "Ada"
role = "admin"
```

**Notes**

- Keys that are absent in the source are skipped; source key order is preserved.

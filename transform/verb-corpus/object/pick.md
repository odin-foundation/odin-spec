# %pick

keep only named keys

**Signature:** `%pick <object> <key...> -> object`

Return a copy of an object containing only the named keys.

**Transform**

```odin
{out}
p = %pick @.rec "name" "role"
```

**In**

```odin
{rec}
name = "Ada"
role = "admin"
active = ?true
```

**Out**

```odin
{out}
{.p}
name = "Ada"
role = "admin"
```

**Notes**

- Keys that are absent in the source are skipped; source key order is preserved.

# %every

do all items match a condition

**Signature:** `%every <array> <string:field> <string:op> <value> -> boolean`

Return true when every record satisfies a field condition.

**Transform**

```odin
{out}
allActive = %every @.coverages "status" "=" "active"
```

**In**

```odin
{coverages[] : type, status}
"liability", "active"
"collision", "active"
```

**Out**

```odin
{out}
allActive = ?true
```

**Notes**

- Field, operator, and value are three separate quoted arguments.
- An empty array returns ?true (vacuous truth).

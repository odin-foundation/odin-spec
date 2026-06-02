# %every

do all items match a condition

**Signature:** `%every <array> <string:field> <string:op> <value> -> boolean`

Return true when every record satisfies a field condition.

**Transform**

```odin
{out}
allActive = %every @.coverages "status" "=" "active"
notAll = %every @.mixed "status" "=" "active"
nonArray = %every @.notArr "status" "=" "active"
```

**In**

```odin
notArr = "x"

{coverages[] : type, status}
"liability", "active"
"collision", "active"

{mixed[] : type, status}
"liability", "active"
"collision", "lapsed"
```

**Out**

```odin
{out}
allActive = ?true
notAll = ?false
nonArray = ~
```

**Notes**

- Field, operator, and value are three separate quoted arguments.
- An empty array returns ?true (vacuous truth).

# %some

does any item match a condition

**Signature:** `%some <array> <string:field> <string:op> <value> -> boolean`

Return true when at least one record satisfies a field condition.

**Transform**

```odin
{out}
hasCancelled = %some @.coverages "status" "=" "cancelled"
```

**In**

```odin
{coverages[] : type, status}
"liability", "active"
"collision", "cancelled"
```

**Out**

```odin
{out}
hasCancelled = ?true
```

**Notes**

- Field, operator, and value are three separate quoted arguments.
- An empty array returns ?false.

# %partition

split by a condition

**Signature:** `%partition <array> <string:field> <string:op> <value> -> array`

Divide an array into [matching, non-matching] by a field condition.

**Transform**

```odin
{out}
split = %partition @.coverages "status" "=" "active"
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
{.split[] : [0].type, .status}
"liability", "active"
"collision", "cancelled"
```

**Notes**

- Returns a two-element array: index 0 is matches, index 1 is the rest.
- Field, operator, and value are three separate quoted arguments.

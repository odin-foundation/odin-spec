# %partition

split by a condition

**Signature:** `%partition <array> <string:field> <string:op> <value> -> array`

Divide an array into [matching, non-matching] by a field condition.

**Transform**

```odin
{out}
split = %partition @.coverages "status" "=" "active"
byAmount = %partition @.coverages "premium" "<" "150"
```

**In**

```odin
{coverages[] : type, status, premium}
"liability", "active", ##200
"collision", "cancelled", ##100
```

**Out**

```odin
{out}
{.split[] : [0].type, .status, .premium}
"liability", "active", ##200
"collision", "cancelled", ##100
{.byAmount[] : [0].type, .status, .premium}
"collision", "cancelled", ##100
"liability", "active", ##200
```

**Notes**

- Returns a two-element array: index 0 is matches, index 1 is the rest.
- Field, operator, and value are three separate quoted arguments.

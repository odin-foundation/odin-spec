# %filter

keep array elements matching a condition

**Signature:** `%filter <array> <string:field> <string:op> <value> -> array`

Filter an array of objects to the rows where a field matches a value.

**Transform**

```odin
{out}
active = %filter @.coverages "status" "=" "active"
greater = %filter @.coverages "limit" ">" ##100
notActive = %filter @.coverages "status" "!=" "active"
```

**In**

```odin
{coverages[] : type, status, limit}
"liability", "active", ##50
"collision", "cancelled", ##200
"comprehensive", "active", ##300
```

**Out**

```odin
{out}
{.active[] : type, status, limit}
"liability", "active", ##50
"comprehensive", "active", ##300
{.greater[] : type, status, limit}
"collision", "cancelled", ##200
"comprehensive", "active", ##300
{.notActive[] : type, status, limit}
"collision", "cancelled", ##200
```

**Notes**

- The field, operator, and value are three separate quoted arguments - not one condition string.
- The kept rows are emitted as a relative tabular sub-block ({.active[] : ...}) under the output section.

**Avoid**

- `active = %filter @.coverages "status = active"`: the condition is three arguments (field, op, value), not one quoted infix string; a single string raises a verb-arity error

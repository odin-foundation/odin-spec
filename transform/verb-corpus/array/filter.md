# %filter

keep array elements matching a condition

**Signature:** `%filter <array> <string:field> <string:op> <value> -> array`

Filter an array of objects to the rows where a field matches a value.

**Transform**

```odin
{out}
active = %filter @.coverages "status" "=" "active"
```

**In**

```odin
{coverages[] : type, status}
"liability", "active"
"collision", "cancelled"
"comprehensive", "active"
```

**Out**

```odin
{out}
{.active[] : type, status}
"liability", "active"
"comprehensive", "active"
```

**Notes**

- The field, operator, and value are three separate quoted arguments - not one condition string.
- The kept rows are emitted as a relative tabular sub-block ({.active[] : ...}) under the output section.

**Avoid**

- `active = %filter @.coverages "status = active"`: the condition is three arguments (field, op, value), not one quoted infix string; a single string raises a verb-arity error

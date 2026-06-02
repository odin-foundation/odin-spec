# %sortBy

sort an object array by a field

**Signature:** `%sortBy <array> <string:field> -> array`

Order an array of records ascending by one named field.

**Transform**

```odin
{out}
ranked = %sortBy @.coverages "premium"
```

**In**

```odin
{coverages[] : type, premium}
"collision", ##300
"liability", ##100
"comprehensive", ##200
```

**Out**

```odin
{out}
{.ranked[] : type, premium}
"liability", ##100
"comprehensive", ##200
"collision", ##300
```

**Notes**

- The field name is a required quoted argument.
- Sorts ascending; use %sort with a "desc" third argument for descending order.

**Avoid**

- `ranked = %sortBy @.coverages`: the field argument is required; omitting it raises a verb-arity error

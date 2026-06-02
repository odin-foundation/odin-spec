# %sortBy

sort an object array by a field

**Signature:** `%sortBy <array> <string:field> -> array`

Order an array of records ascending by one named field.

**Transform**

```odin
{out}
ranked = %sortBy @.coverages "premium"
byType = %sortBy @.coverages "type"
single = %sortBy @.solo "premium"
```

**In**

```odin
{coverages[] : type, premium}
"collision", ##300
"liability", ##100
"comprehensive", ##200

{solo[] : type, premium}
"umbrella", ##500
```

**Out**

```odin
{out}
{.ranked[] : type, premium}
"liability", ##100
"comprehensive", ##200
"collision", ##300
{.byType[] : type, premium}
"collision", ##300
"comprehensive", ##200
"liability", ##100
{.single[] : type, premium}
"umbrella", ##500
```

**Notes**

- The field name is a required quoted argument.
- Sorts ascending; use %sort with a "desc" third argument for descending order.

**Avoid**

- `ranked = %sortBy @.coverages`: the field argument is required; omitting it raises a verb-arity error

# %explode

one row per array element

**Signature:** `%explode <array> <field> -> array`

Expand an array-valued field, emitting one row per element with that field replaced by the element.

**Transform**

```odin
{out}
rows = %explode @.orders "tags"
```

**In**

```odin
{orders[0]}
id = "o1"
{.tags[] : ~}
"red"
"blue"

{orders[1]}
id = "o2"
{.tags[] : ~}
```

**Out**

```odin
{out}
{.rows[] : id, tags}
"o1", "red"
"o1", "blue"
"o2",
```

**Notes**

- Rows whose field is missing or empty are emitted once, unchanged.

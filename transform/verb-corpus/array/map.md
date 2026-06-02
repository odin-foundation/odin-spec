# %map

extract one field from each record

**Signature:** `%map <array> <string:field> -> array`

Project a single field out of every object into a flat array.

**Transform**

```odin
{out}
types = %map @.coverages "type"
```

**In**

```odin
{coverages[] : type, premium}
"liability", ##100
"collision", ##300
```

**Out**

```odin
{out}
{.types[] : ~}
"liability"
"collision"
```

**Notes**

- Returns a primitive array of the chosen field values, in source order.

# %map

extract one field from each record

**Signature:** `%map <array> <string:field> -> array`

Project a single field out of every object into a flat array.

**Transform**

```odin
{out}
types = %map @.coverages "type"
missing = %map @.coverages "premium"
```

**In**

```odin
{coverages[] : type}
"liability"
"collision"
```

**Out**

```odin
{out}
{.types[] : ~}
"liability"
"collision"
{.missing[] : ~}
~
~
```

**Notes**

- Returns a primitive array of the chosen field values, in source order.

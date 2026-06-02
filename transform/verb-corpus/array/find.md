# %find

first record matching a condition

**Signature:** `%find <array> <string:field> <string:op> <value> -> object`

Return the first record where a field matches, or null.

**Transform**

```odin
{out}
{.first} = %find @.coverages "status" "=" "active"
```

**In**

```odin
{coverages[] : type, status}
"liability", "cancelled"
"collision", "active"
```

**Out**

```odin
{out}
{.first}
type = "collision"
status = "active"
```

**Notes**

- Returns a single object, emitted as a relative sub-section ({.first}).
- No match returns null (~).

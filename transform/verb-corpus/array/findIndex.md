# %findIndex

index of first matching record

**Signature:** `%findIndex <array> <string:field> <string:op> <value> -> integer`

Return the index of the first record matching a condition, or -1.

**Transform**

```odin
{out}
idx = %findIndex @.coverages "status" "=" "active"
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
idx = ##1
```

**Notes**

- Indices are zero-based; no match returns ##-1.

# %groupBy

group records by a field

**Signature:** `%groupBy <array> <string:field> -> array`

Bucket an object array into { key, items } groups by a field value.

**Transform**

```odin
{out}
groups = %groupBy @.orders "status"
```

**In**

```odin
{orders[] : id, status}
"o1", "active"
"o2", "pending"
"o3", "active"
```

**Out**

```odin
{out}
{out.groups[0]}
key = "active"
{.items[] : id, status}
"o1", "active"
"o3", "active"
{out.groups[1]}
key = "pending"
{.items[] : id, status}
"o2", "pending"
```

**Notes**

- Each group is an object with a key and an items array.
- Groups appear in first-seen key order.

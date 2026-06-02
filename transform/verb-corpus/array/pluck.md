# %pluck

extract a field from each record

**Signature:** `%pluck <array> <string:field> -> array`

Collect one field value from every object into an array.

**Transform**

```odin
{out}
ids = %pluck @.users "id"
```

**In**

```odin
{users[] : id, name}
"u1", "alice"
"u2", "bob"
```

**Out**

```odin
{out}
{.ids[] : ~}
"u1"
"u2"
```

**Notes**

- Behaves like %map: projects the named field into a flat array.

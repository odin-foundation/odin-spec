# %keyBy

index items by a field

**Signature:** `%keyBy <array> <field> -> object`

Build an object that maps each item field value to the item.

**Transform**

```odin
{out}
byId = %keyBy @.users "id"
```

**In**

```odin
{users[] : id, name}
"u1", "Ada"
"u2", "Bo"
"u1", "Ada2"
```

**Out**

```odin
{out}
{.byId}
{out.byId.u1}
id = "u1"
name = "Ada2"
{out.byId.u2}
id = "u2"
name = "Bo"
```

**Notes**

- On duplicate field values the last item wins.

# %entries

key/value pairs of an object

**Signature:** `%entries <object> -> array`

Return an object as an array of [key, value] pairs.

**Transform**

```odin
{out}
e = %entries @.obj
```

**In**

```odin
{obj}
name = "Ada"
role = "admin"
active = ?true
```

**Out**

```odin
{out}
{.e[] : [0], [1]}
"name", "Ada"
"role", "admin"
"active", ?true
```

**Notes**

- The single argument is an object; the result is an array of two-element pairs serialized as a {.e[] : [0], [1]} table.
- Each row is key then value, preserving the value's type and source key order.
- A non-object argument resolves to ~.

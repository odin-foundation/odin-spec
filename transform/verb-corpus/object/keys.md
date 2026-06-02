# %keys

property names of an object

**Signature:** `%keys <object> -> array`

Return the own enumerable property names of an object as an array.

**Transform**

```odin
{out}
k = %keys @.obj
nonObject = %keys @.notObj
```

**In**

```odin
notObj = "x"

{obj}
name = "Ada"
role = "admin"
active = ?true
```

**Out**

```odin
{out}
{.k[] : ~}
"name"
"role"
"active"
nonObject = ~
```

**Notes**

- The single argument is an object; the result is an array of key strings serialized as a {.k[] : ~} block.
- Keys preserve source order; prototype-pollution keys (__proto__, constructor, prototype) are excluded.
- A non-object argument resolves to ~.

# %values

property values of an object

**Signature:** `%values <object> -> array`

Return the own enumerable property values of an object as an array.

**Transform**

```odin
{out}
v = %values @.obj
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
{.v[] : ~}
"Ada"
"admin"
?true
```

**Notes**

- The single argument is an object; the result is an array of values serialized as a {.v[] : ~} block, preserving each value's type.
- Values follow source key order; prototype-pollution keys are excluded.
- A non-object argument resolves to ~.

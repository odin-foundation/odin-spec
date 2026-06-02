# %has

test for a property

**Signature:** `%has <object> <string:key> -> boolean`

Return true when an object contains a key, with dot notation for nested paths.

**Transform**

```odin
{out}
h = %has @.obj "role"
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
h = ?true
```

**Notes**

- The second argument is a quoted key; the result is a boolean (?).
- Dot notation walks nested objects ("a.b.c"); missing keys and prototype-pollution keys yield ?false.
- A non-object first argument yields ?false.

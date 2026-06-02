# %isArray

array-type test

**Signature:** `%isArray <value> -> boolean`

Return true when a value is an array.

**Transform**

```odin
{out}
isList = %isArray @.tags
notList = %isArray @.name
```

**In**

```odin
name = "Ada"

{tags[] : ~}
"a"
"b"
```

**Out**

```odin
{out}
isList = ?true
notList = ?false
```

**Notes**

- The source declares the array with a {tags[] : ~} block of one value per line.
- Distinguishes arrays from objects; use %isObject for the latter.

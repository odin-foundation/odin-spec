# %get

read a property with an optional default

**Signature:** `%get <object> <string:path> [<default>] -> value`

Read a value at a key path from an object, falling back to a default when absent.

**Transform**

```odin
{out}
g = %get @.obj "role"
nested = %get @.obj "addr.city"
missing = %get @.obj "zzz"
fallback = %get @.obj "zzz" "n/a"
nonObject = %get @.notObj "role"
```

**In**

```odin
notObj = "x"

{obj}
name = "Ada"
role = "admin"
active = ?true

{obj.addr}
city = "Reno"
```

**Out**

```odin
{out}
g = "admin"
nested = "Reno"
missing = ~
fallback = "n/a"
nonObject = ~
```

**Notes**

- The second argument is a quoted key or dot path ("a.b.c"); the resolved value keeps its type.
- An optional third argument is returned when the path is missing or references an unsafe property; with no default a miss yields ~.
- A non-object first argument returns the default (or ~).

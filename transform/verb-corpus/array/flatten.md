# %flatten

flatten nested arrays one level

**Signature:** `%flatten <array> -> array`

Collapse an array of arrays into a single array, one level deep.

**Transform**

```odin
{out}
all = %flatten @.nested
```

**In**

```odin
nested = "[[1, 2], [3, 4]]"
```

**Out**

```odin
{out}
{.all[] : ~}
##1
##2
##3
##4
```

**Notes**

- A nested (2D) array is supplied as a JSON-array string literal; the engine parses it into nested arrays before flattening.
- Flattening is one level deep only - deeper nesting is preserved.

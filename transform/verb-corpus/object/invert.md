# %invert

swap keys and values

**Signature:** `%invert <object> -> object`

Return an object with keys and values swapped.

**Transform**

```odin
{out}
inv = %invert @.m
```

**In**

```odin
{m}
a = "x"
b = "y"
```

**Out**

```odin
{out}
{.inv}
x = "a"
y = "b"
```

**Notes**

- Values are coerced to strings to become keys; duplicate values collapse to the last key.

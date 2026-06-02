# %invert

swap keys and values

**Signature:** `%invert <object> -> object`

Return an object with keys and values swapped.

**Transform**

```odin
{out}
swapped = %invert @.m
dupLastWins = %invert @.dup
```

**In**

```odin
{m}
a = "x"
b = "y"

{dup}
a = "same"
b = "same"
```

**Out**

```odin
{out}
dupLastWins.same = "b"
{.swapped}
x = "a"
y = "b"
```

**Notes**

- Values are coerced to strings to become keys; duplicate values collapse to the last key.

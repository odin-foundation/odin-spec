# %abs

absolute value

**Signature:** `%abs <number> -> number`

Return the absolute value of a number.

**Transform**

```odin
{out}
v = %abs @.a
```

**In**

```odin
a = #-7.25
```

**Out**

```odin
{out}
v = #7.25
```

**Notes**

- A fractional input stays a number (#); a whole-valued result would emit as an integer (##).

**Avoid**

- `v = %abs`: the value argument is required; omitting it raises a verb-arity error

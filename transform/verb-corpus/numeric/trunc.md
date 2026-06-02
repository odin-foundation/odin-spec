# %trunc

truncate toward zero

**Signature:** `%trunc <number> -> integer`

Discard the fractional part of a number, rounding toward zero.

**Transform**

```odin
{out}
v = %trunc @.a
```

**In**

```odin
a = #-3.7
```

**Out**

```odin
{out}
v = ##-3
```

**Notes**

- Rounds toward zero (-3.7 → -3), unlike %floor which rounds toward negative infinity (-3.7 → -4).
- Always returns an integer (##).

# %trunc

truncate toward zero

**Signature:** `%trunc <number> -> integer`

Discard the fractional part of a number, rounding toward zero.

**Transform**

```odin
{out}
v = %trunc @.a
positive = %trunc @.b
whole = %trunc @.c
```

**In**

```odin
a = #-3.7
b = #3.9
c = #5
```

**Out**

```odin
{out}
v = ##-3
positive = ##3
whole = ##5
```

**Notes**

- Rounds toward zero (-3.7 -> -3), unlike %floor which rounds toward negative infinity (-3.7 -> -4).
- Always returns an integer (##).

# %sign

sign of a number

**Signature:** `%sign <number> -> integer`

Return -1, 0, or 1 according to the sign of a number.

**Transform**

```odin
{out}
v = %sign @.a
positive = %sign @.b
zero = %sign @.c
```

**In**

```odin
a = #-3.5
b = #42
c = #0
```

**Out**

```odin
{out}
v = ##-1
positive = ##1
zero = ##0
```

**Notes**

- Returns an integer (##): -1 for negatives, 1 for positives, 0 for zero.

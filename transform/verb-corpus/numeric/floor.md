# %floor

round down to integer

**Signature:** `%floor <number> -> integer`

Round a number down toward negative infinity, returning an integer.

**Transform**

```odin
{out}
v = %floor @.a
```

**In**

```odin
a = #3.7
```

**Out**

```odin
{out}
v = ##3
```

**Notes**

- Always returns an integer (##), even for a fractional input.
- Rounds toward negative infinity, so -3.2 would floor to -4 (use %trunc to round toward zero).

# %floor

round down to integer

**Signature:** `%floor <number> -> integer`

Round a number down toward negative infinity, returning an integer.

**Transform**

```odin
{out}
v = %floor @.a
negative = %floor @.neg
already = %floor @.whole
ofNull = %floor @.nul
```

**In**

```odin
a = #3.7
neg = #-3.2
whole = ##5
nul = ~
```

**Out**

```odin
{out}
v = ##3
negative = ##-4
already = ##5
ofNull = ##0
```

**Notes**

- Always returns an integer (##), even for a fractional input.
- Rounds toward negative infinity, so -3.2 would floor to -4 (use %trunc to round toward zero).

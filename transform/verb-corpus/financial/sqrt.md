# %sqrt

square root

**Signature:** `%sqrt <number:value> -> number`

Take the square root of a non-negative value.

**Transform**

```odin
{out}
r = %sqrt @.x
perfect = %sqrt @.nine
zero = %sqrt @.zero
negative = %sqrt @.neg
```

**In**

```odin
x = #2
nine = #9
zero = #0
neg = #-4
```

**Out**

```odin
{out}
r = #1.4142135623730951
perfect = ##3
zero = ##0
negative = ~
```

**Notes**

- The full-precision float is preserved in the output; a perfect square (9) returns the exact integer 3, and 0 returns 0.
- A negative value yields ~ (null).

**Avoid**

- `r = %sqrt @.x ; x = #-4`: a negative radicand yields ~ (null), not a complex or NaN result

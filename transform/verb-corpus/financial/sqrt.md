# %sqrt

square root

**Signature:** `%sqrt <number:value> -> number`

Take the square root of a non-negative value.

**Transform**

```odin
{out}
r = %sqrt @.x
```

**In**

```odin
x = #2
```

**Out**

```odin
{out}
r = #1.4142135623730951
```

**Notes**

- The full-precision float is preserved in the output.
- A negative value yields ~ (null).

**Avoid**

- `r = %sqrt @.x ; x = #-4`: a negative radicand yields ~ (null), not a complex or NaN result

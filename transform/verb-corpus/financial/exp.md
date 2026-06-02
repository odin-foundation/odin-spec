# %exp

exponential (e^x)

**Signature:** `%exp <number:value> -> number`

Raise Euler's number e to the given power.

**Transform**

```odin
{out}
r = %exp @.one
zero = %exp @.zero
negative = %exp @.neg
```

**In**

```odin
one = #1
zero = #0
neg = #-1
```

**Out**

```odin
{out}
r = #2.718281828459045
zero = ##1
negative = #0.36787944117144233
```

**Notes**

- exp(1) is e, the base of the natural logarithm.
- The full-precision float is preserved in the output.

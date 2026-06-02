# %exp

exponential (e^x)

**Signature:** `%exp <number:value> -> number`

Raise Euler's number e to the given power.

**Transform**

```odin
{out}
r = %exp @.x
```

**In**

```odin
x = #1
```

**Out**

```odin
{out}
r = #2.718281828459045
```

**Notes**

- exp(1) is e, the base of the natural logarithm.
- The full-precision float is preserved in the output.

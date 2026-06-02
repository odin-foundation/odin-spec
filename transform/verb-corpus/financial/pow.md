# %pow

raise base to an exponent

**Signature:** `%pow <number:base> <number:exponent> -> number`

Raise a base to an exponent (e.g. a growth factor over n periods).

**Transform**

```odin
{out}
r = %pow @.base @.exp
```

**In**

```odin
base = #1.05
exp = #10
```

**Out**

```odin
{out}
r = #1.6288946267774422
```

**Notes**

- 1.05^10 is the ten-period growth factor for a 5% rate.
- A non-finite result (e.g. overflow) yields ~ (null).
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.

**Avoid**

- `r = %pow @.base`: %pow needs both base and exponent; one argument yields ~ (null)

# %coerceNumber

parse a value to a number

**Signature:** `%coerceNumber <value> -> number`

Convert a string (or other value) to a typed number, keeping the fractional part.

**Transform**

```odin
{out}
n = %coerceNumber @.raw
```

**In**

```odin
raw = "3.14"
```

**Out**

```odin
{out}
n = #3.14
```

**Notes**

- The quoted string "3.14" becomes the typed number #3.14.
- A whole-number input like "42" emits as ##42 (integer prefix) because the value has no fractional part - coerceNumber preserves fractions but collapses whole values to integers.
- A non-numeric input coerces to ##0 rather than raising an error - validate upstream if that matters.

**Avoid**

- `n = %coerceNumber @.raw ; input "3.9" expecting ##3`: coerceNumber keeps the fraction (#3.9); use %coerceInteger to floor to ##3

# %formatCurrency

two-decimal currency string

**Signature:** `%formatCurrency <number> -> string`

Format a number as a currency string with exactly two decimal places.

**Transform**

```odin
{out}
v = %formatCurrency @.a
```

**In**

```odin
a = #19.5
```

**Out**

```odin
{out}
v = "19.50"
```

**Notes**

- Always emits two decimals (19.5 → "19.50") and produces a plain string with no currency symbol.
- The output is a string, not the #$ currency value type.

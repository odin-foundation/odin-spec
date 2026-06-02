# %formatCurrency

two-decimal currency string

**Signature:** `%formatCurrency <number> -> string`

Format a number as a currency string with exactly two decimal places.

**Transform**

```odin
{out}
v = %formatCurrency @.a
whole = %formatCurrency @.b
negative = %formatCurrency @.c
rounds = %formatCurrency @.d
```

**In**

```odin
a = #19.5
b = ##42
c = #-3.1
d = #2.567
```

**Out**

```odin
{out}
v = "19.50"
whole = "42.00"
negative = "-3.10"
rounds = "2.57"
```

**Notes**

- Always emits two decimals (19.5 → "19.50") and produces a plain string with no currency symbol.
- The output is a string, not the #$ currency value type.

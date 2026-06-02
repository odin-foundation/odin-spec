# %formatInteger

floor to integer string

**Signature:** `%formatInteger <number> -> string`

Format a number as an integer string by flooring it.

**Transform**

```odin
{out}
v = %formatInteger @.a
negative = %formatInteger @.neg
already = %formatInteger @.whole
```

**In**

```odin
a = #42.9
neg = #-2.1
whole = ##1000
```

**Out**

```odin
{out}
v = "42"
negative = "-3"
already = "1000"
```

**Notes**

- Flooring discards the fraction (42.9 -> 42), it does not round.
- The result is a string, not a number type.

# %formatInteger

floor to integer string

**Signature:** `%formatInteger <number> -> string`

Format a number as an integer string by flooring it.

**Transform**

```odin
{out}
v = %formatInteger @.a
```

**In**

```odin
a = #42.9
```

**Out**

```odin
{out}
v = "42"
```

**Notes**

- Flooring discards the fraction (42.9 → 42), it does not round.
- The result is a string, not a number type.

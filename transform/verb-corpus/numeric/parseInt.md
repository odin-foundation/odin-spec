# %parseInt

parse string to integer with radix

**Signature:** `%parseInt <string> [integer:radix] -> integer`

Parse a string into an integer using an explicit numeric base.

**Transform**

```odin
{out}
v = %parseInt @.a ##16
```

**In**

```odin
a = "FF"
```

**Out**

```odin
{out}
v = ##255
```

**Notes**

- The radix is optional and defaults to 10; here base 16 parses "FF" to 255.
- A radix must be between 2 and 36, otherwise the result is null (~).

**Avoid**

- `v = %parseInt @.a`: without a radix the default base 10 is used, so a hex string like "FF" parses to null (~) rather than 255

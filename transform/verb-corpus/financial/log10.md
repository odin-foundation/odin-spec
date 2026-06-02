# %log10

base-10 logarithm

**Signature:** `%log10 <number:value> -> number`

Take the base-10 logarithm of a value.

**Transform**

```odin
{out}
r = %log10 @.x
```

**In**

```odin
x = #1000
```

**Out**

```odin
{out}
r = ##3
```

**Notes**

- log10(1000) is 3.
- A value <= 0 yields ~ (null).

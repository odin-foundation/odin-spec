# %log10

base-10 logarithm

**Signature:** `%log10 <number:value> -> number`

Take the base-10 logarithm of a value.

**Transform**

```odin
{out}
r = %log10 @.thousand
one = %log10 @.one
zero = %log10 @.zero
negative = %log10 @.neg
```

**In**

```odin
thousand = #1000
one = #1
zero = #0
neg = #-5
```

**Out**

```odin
{out}
r = ##3
one = ##0
zero = ~
negative = ~
```

**Notes**

- log10(1000) is 3.
- A value <= 0 yields ~ (null).

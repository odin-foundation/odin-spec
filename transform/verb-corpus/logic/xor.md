# %xor

exclusive OR

**Signature:** `%xor <a:boolean> <b:boolean> -> boolean`

Return true when exactly one of two conditions is truthy.

**Transform**

```odin
{out}
exactlyOne = %xor @.hasEmail @.hasPhone
both = %xor @.hasEmail @.hasFax
neither = %xor @.hasPhone @.hasSms
```

**In**

```odin
hasEmail = ?true
hasPhone = ?false
hasFax = ?true
hasSms = ?false
```

**Out**

```odin
{out}
exactlyOne = ?true
both = ?false
neither = ?false
```

**Notes**

- True when the operands differ; false when both are true or both are false.
- Two operands, both coerced to booleans.

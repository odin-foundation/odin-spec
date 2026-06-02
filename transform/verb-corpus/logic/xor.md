# %xor

exclusive OR

**Signature:** `%xor <a:boolean> <b:boolean> -> boolean`

Return true when exactly one of two conditions is truthy.

**Transform**

```odin
{out}
exactlyOne = %xor @.hasEmail @.hasPhone
```

**In**

```odin
hasEmail = ?true
hasPhone = ?false
```

**Out**

```odin
{out}
exactlyOne = ?true
```

**Notes**

- True when the operands differ; false when both are true or both are false.
- Two operands, both coerced to booleans.

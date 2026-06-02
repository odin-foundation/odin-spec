# %not

boolean NOT

**Signature:** `%not <value:boolean> -> boolean`

Return the logical negation of a boolean value.

**Transform**

```odin
{out}
inactive = %not @.active
```

**In**

```odin
active = ?true
```

**Out**

```odin
{out}
inactive = ?false
```

**Notes**

- Single operand; negates its truthiness.
- Often wraps a comparison: %not %eq @.status "closed".

**Avoid**

- `inactive = %not "false"`: the literal string "false" is not one of the recognized truthy tokens (true/yes/y/1), so it is treated as falsy and %not returns ?true - the opposite of intent. Negate an actual boolean path or verb, e.g. %not @.active.

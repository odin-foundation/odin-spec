# %eq

equality test

**Signature:** `%eq <a:value> <b:value> -> boolean`

Return true when two values are equal, as a boolean usable as a condition.

**Transform**

```odin
{out}
same = %eq @.status "active"
```

**In**

```odin
status = "active"
```

**Out**

```odin
{out}
same = ?true
```

**Notes**

- Both arguments are verb-expressions or paths/literals - never a quoted infix like "@.status == 'active'".
- The boolean result drops straight into a condition slot, e.g. %ifElse %eq @.status "active" ... .

**Avoid**

- `match = %eq @.code "200"`: an integer source code (##200) compares equal to the string "200": cross-type numeric/string operands are coerced before comparison, so this fires even though one side is text. Quote only when both sides are genuinely strings.

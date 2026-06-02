# %assert

validate a condition

**Signature:** `%assert <condition:boolean> [<message:string>] -> value`

Return the condition value when truthy, otherwise null, for inline validation.

**Transform**

```odin
{out}
valid = %assert %gt @.amount ##0
failed = %assert %gt @.zero ##0
passThrough = %assert %eq @.status "active"
```

**In**

```odin
amount = ##150
zero = ##0
status = "active"
```

**Out**

```odin
{out}
valid = ?true
failed = ~
passThrough = ?true
```

**Notes**

- Passes the condition through when truthy; a failing assertion yields null (~) and the transform continues.
- An optional second string argument is a diagnostic message and does not appear in the output.

**Avoid**

- `valid = %assert "@.amount > 0"`: the quoted infix string is a literal, and a non-empty arbitrary string is not one of the truthy tokens, so the assertion fails and yields null. Pass a real condition like %gt @.amount ##0.

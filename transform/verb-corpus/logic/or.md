# %or

boolean OR

**Signature:** `%or <a:boolean> <b:boolean> -> boolean`

Return true when either condition is truthy.

**Transform**

```odin
{out}
either = %or %eq @.role "admin" %eq @.role "owner"
neither = %or %eq @.role "admin" %eq @.role "editor"
```

**In**

```odin
role = "owner"
```

**Out**

```odin
{out}
either = ?true
neither = ?false
```

**Notes**

- Both operands are %eq verb-expressions producing booleans.
- Exactly two operands; nest %or for additional alternatives.

**Avoid**

- `either = %or "@.role == 'admin'" "@.role == 'owner'"`: the quoted infix expressions are inert literal strings - not truthy - so %or returns ?false and the rule never fires. Build each side from a comparison verb like %eq @.role "admin".

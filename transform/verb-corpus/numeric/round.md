# %round

round to N decimal places

**Signature:** `%round <number> <integer:places> -> number`

Round a number to a fixed number of decimal places.

**Transform**

```odin
{out}
amount = %round @.amount ##2
```

**In**

```odin
amount = #123.456789
```

**Out**

```odin
{out}
amount = #123.46
```

**Notes**

- The second argument is the decimal-place count and must be an integer literal (##2).
- A fractional input stays a number (#) in the output.

**Avoid**

- `amount = %round @.amount`: the decimal-places argument is required; omitting it raises a verb-arity error

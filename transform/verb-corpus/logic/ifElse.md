# %ifElse

choose a value on a condition

**Signature:** `%ifElse <condition> <then> <else> -> value`

Return one of two values depending on a boolean condition.

**Transform**

```odin
{out}
tier = %ifElse %gt @.amount ##1000 "VIP" "Standard"
elseBranch = %ifElse %gt @.small ##1000 "VIP" "Standard"
```

**In**

```odin
amount = ##2500
small = ##50
```

**Out**

```odin
{out}
tier = "VIP"
elseBranch = "Standard"
```

**Notes**

- The condition is a verb-expression (%gt @.amount ##1000), evaluated left-to-right - not a quoted infix string.
- ifElse calls nest: the else branch can be another %ifElse for multi-way classification.

**Avoid**

- `tier = %ifElse "@.amount > 1000" "VIP" "Standard"`: a quoted string is a literal, always truthy-or-not as data - it is never parsed as a condition; the comparison silently never fires. Use %gt @.amount ##1000

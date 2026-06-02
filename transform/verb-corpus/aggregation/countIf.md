# %countIf

count items matching a condition

**Signature:** `%countIf <array> <field> <op> <value> -> integer`

Count the array items whose field satisfies an operator/value condition.

**Transform**

```odin
{out}
paid = %countIf @.orders "status" "=" "paid"
```

**In**

```odin
{orders[] : status, amount}
"paid", ##100
"open", ##200
"paid", ##300
```

**Out**

```odin
{out}
paid = ##2
```

**Notes**

- Operators match %filter: =, !=, <, <=, >, >=, contains, startsWith, endsWith.

# %avgIf

average a field over matching items

**Signature:** `%avgIf <array> <field> <op> <value> [<avgField>] -> number`

Average a field over the array items that satisfy a condition; the average field defaults to the predicate field.

**Transform**

```odin
{out}
paidAvg = %avgIf @.orders "status" "=" "paid" "amount"
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
paidAvg = ##200
```

**Notes**

- Returns ~ when no item matches.
- Operators match %filter.

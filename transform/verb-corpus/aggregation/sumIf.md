# %sumIf

sum a field over matching items

**Signature:** `%sumIf <array> <field> <op> <value> [<sumField>] -> number`

Sum a field over the array items that satisfy a condition; the sum field defaults to the predicate field.

**Transform**

```odin
{out}
paidTotal = %sumIf @.orders "status" "=" "paid" "amount"
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
paidTotal = ##400
```

**Notes**

- Without a fifth argument the predicate field itself is summed.
- Operators match %filter.

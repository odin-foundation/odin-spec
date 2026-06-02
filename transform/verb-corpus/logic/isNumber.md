# %isNumber

numeric-type test

**Signature:** `%isNumber <value> -> boolean`

Return true when a value is integer, number, or currency.

**Transform**

```odin
{out}
isNum = %isNumber @.amount
```

**In**

```odin
amount = ##42
```

**Out**

```odin
{out}
isNum = ?true
```

**Notes**

- True for integer (##), number (#), and currency (#$) values alike.
- A numeric string like "42" is a string, not a number - coerce with %coerceNumber first if needed.

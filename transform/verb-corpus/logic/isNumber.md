# %isNumber

numeric-type test

**Signature:** `%isNumber <value> -> boolean`

Return true when a value is integer, number, or currency.

**Transform**

```odin
{out}
isNum = %isNumber @.amount
isFloat = %isNumber @.rate
numericText = %isNumber @.code
nullVal = %isNumber @.missing
```

**In**

```odin
amount = ##42
rate = #3.14
code = "42"
missing = ~
```

**Out**

```odin
{out}
isNum = ?true
isFloat = ?true
numericText = ?false
nullVal = ?false
```

**Notes**

- True for integer (##), number (#), and currency (#$) values alike.
- A numeric string like "42" is a string, not a number - coerce with %coerceNumber first if needed.

# %isString

string-type test

**Signature:** `%isString <value> -> boolean`

Return true when a value is of string type.

**Transform**

```odin
{out}
isText = %isString @.name
empty = %isString @.blank
numeric = %isString @.amount
nullVal = %isString @.missing
```

**In**

```odin
name = "Ada"
blank = ""
amount = ##5
missing = ~
```

**Out**

```odin
{out}
isText = ?true
empty = ?true
numeric = ?false
nullVal = ?false
```

**Notes**

- Tests the value's type, not its content; a numeric ##5 returns ?false.
- Pair with %typeOf when you need the type name rather than a yes/no answer.

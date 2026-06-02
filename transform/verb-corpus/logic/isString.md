# %isString

string-type test

**Signature:** `%isString <value> -> boolean`

Return true when a value is of string type.

**Transform**

```odin
{out}
isText = %isString @.name
```

**In**

```odin
name = "Ada"
```

**Out**

```odin
{out}
isText = ?true
```

**Notes**

- Tests the value's type, not its content; a numeric ##5 returns ?false.
- Pair with %typeOf when you need the type name rather than a yes/no answer.

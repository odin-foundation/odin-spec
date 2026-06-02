# %isBoolean

boolean-type test

**Signature:** `%isBoolean <value> -> boolean`

Return true when a value is of boolean type.

**Transform**

```odin
{out}
isBool = %isBoolean @.active
```

**In**

```odin
active = ?true
```

**Out**

```odin
{out}
isBool = ?true
```

**Notes**

- Tests the type only; the string "true" is a string, not a boolean.
- Use %coerceBoolean to turn truthy strings into real booleans.

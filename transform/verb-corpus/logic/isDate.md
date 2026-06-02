# %isDate

date-type test

**Signature:** `%isDate <value> -> boolean`

Return true when a value is a date or timestamp type.

**Transform**

```odin
{out}
isDt = %isDate %coerceDate @.born
notDate = %isDate @.born
```

**In**

```odin
born = "2020-01-15"
```

**Out**

```odin
{out}
isDt = ?true
notDate = ?false
```

**Notes**

- True for both date and timestamp types.
- An ISO string arriving as text is a string, not a date - %coerceDate promotes it to a real date first.

**Avoid**

- `isDt = %isDate @.born`: when born arrives as the plain string "2020-01-15" its type is string, so %isDate returns ?false despite looking date-like. Coerce with %coerceDate before testing.

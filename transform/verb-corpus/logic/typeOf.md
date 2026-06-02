# %typeOf

type name of a value

**Signature:** `%typeOf <value> -> string`

Return the type name of a value as a string.

**Transform**

```odin
{out}
kind = %typeOf @.amount
```

**In**

```odin
amount = ##42
```

**Out**

```odin
{out}
kind = "integer"
```

**Notes**

- Returns one of: null, string, integer, number, currency, boolean, date, timestamp, array, object, and more.
- Use it for routing logic when you need the type name rather than a boolean check.

**Avoid**

- `kind = %typeOf @.born`: an ISO date supplied as the bare string "2020-01-15" reports "string", not "date", because it arrives untyped. Coerce with %coerceDate first if you expect a date type.

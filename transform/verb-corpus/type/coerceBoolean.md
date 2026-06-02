# %coerceBoolean

convert a value to a boolean

**Signature:** `%coerceBoolean <value> -> boolean`

Coerce truthy/falsy text or numbers to a typed boolean.

**Transform**

```odin
{out}
b = %coerceBoolean @.raw
fromOne = %coerceBoolean @.one
fromZero = %coerceBoolean @.zero
fromOther = %coerceBoolean @.other
```

**In**

```odin
raw = "yes"
one = ##1
zero = ##0
other = "maybe"
```

**Out**

```odin
{out}
b = ?true
fromOne = ?true
fromZero = ?false
fromOther = ?false
```

**Notes**

- Truthy strings are "true", "yes", "y", and "1" (case-insensitive); everything else is ?false.
- Numbers coerce by zero/non-zero, so 0 is ?false and any other number is ?true.

**Avoid**

- `b = %coerceBoolean @.raw ; input "maybe"`: any string outside the truthy set silently becomes ?false, not an error

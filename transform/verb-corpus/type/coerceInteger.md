# %coerceInteger

parse a value to an integer

**Signature:** `%coerceInteger <value> -> integer`

Convert a string (or number) to a typed integer.

**Transform**

```odin
{out}
count = %coerceInteger @.countText
```

**In**

```odin
countText = "42"
```

**Out**

```odin
{out}
count = ##42
```

**Notes**

- The output is a true integer (##), so downstream numeric verbs treat it as a number.
- A non-numeric input coerces to ##0 rather than raising an error - validate upstream if that matters.

**Avoid**

- `count = %coerceInteger @.countText ; input "not-a-number"`: unparseable input silently becomes ##0; there is no error to catch

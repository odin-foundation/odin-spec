# %coerceString

convert any value to a string

**Signature:** `%coerceString <value> -> string`

Force a value to a quoted string regardless of its source type.

**Transform**

```odin
{out}
s = %coerceString @.raw
```

**In**

```odin
raw = ##42
```

**Out**

```odin
{out}
s = "42"
```

**Notes**

- The typed integer ##42 loses its # prefix and becomes the quoted string "42".
- A null input coerces to the empty string "" rather than passing ~ through.

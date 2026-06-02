# %length

character count of a string

**Signature:** `%length <string> -> integer`

Return the number of characters in a string.

**Transform**

```odin
{out}
out = %length @.name
empty = %length @.empty
spaces = %length @.spaces
```

**In**

```odin
name = "Honda"
empty = ""
spaces = "a b c"
```

**Out**

```odin
{out}
out = ##5
empty = ##0
spaces = ##5
```

**Notes**

- Returns an integer (## prefix in ODIN output).
- Counts UTF-16 code units, matching the underlying string length.

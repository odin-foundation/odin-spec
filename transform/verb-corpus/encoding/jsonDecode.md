# %jsonDecode

Parse JSON text into a value

**Signature:** `%jsonDecode <string> -> value`

Parse a JSON object or array string into a structured ODIN value.

**Transform**

```odin
{out}
d = %jsonDecode @.j
```

**In**

```odin
j = "{\"name\":\"John\",\"age\":42}"
```

**Out**

```odin
{out}
{.d}
name = "John"
age = ##42
```

**Notes**

- A decoded object becomes a nested block, so the target field d renders as the {.d} subsection.
- Strings that are not JSON objects/arrays are treated as escaped text and unescaped in place.

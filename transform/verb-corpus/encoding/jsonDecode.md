# %jsonDecode

Parse JSON text into a value

**Signature:** `%jsonDecode <string> -> value`

Parse a JSON object or array string into a structured ODIN value.

**Transform**

```odin
{out}
d = %jsonDecode @.j
text = %jsonDecode @.t
bad = %jsonDecode @.x
```

**In**

```odin
j = "{\"name\":\"John\",\"age\":42}"
t = "line1\\nline2"
x = "bad\\xescape"
```

**Out**

```odin
{out}
text = "line1\nline2"
bad = ~
{.d}
name = "John"
age = ##42
```

**Notes**

- A decoded object becomes a nested block, so the target field d renders as the {.d} subsection.
- Strings that are not JSON objects/arrays are treated as escaped text and unescaped in place.

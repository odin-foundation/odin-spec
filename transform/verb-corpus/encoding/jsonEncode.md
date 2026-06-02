# %jsonEncode

Serialize a value to JSON text

**Signature:** `%jsonEncode <value> -> string`

Serialize an object or array into a compact JSON string.

**Transform**

```odin
{out}
j = %jsonEncode @.data
```

**In**

```odin
data.name = "John"
data.age = ##42
```

**Out**

```odin
{out}
j = "{\"name\":\"John\",\"age\":42}"
```

**Notes**

- Objects and arrays produce their full JSON serialization as a single string.
- For a scalar string, the result is the JSON-escaped body without the surrounding quotes.

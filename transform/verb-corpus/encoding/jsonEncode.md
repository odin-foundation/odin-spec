# %jsonEncode

Serialize a value to JSON text

**Signature:** `%jsonEncode <value> -> string`

Serialize an object or array into a compact JSON string.

**Transform**

```odin
{out}
j = %jsonEncode @.data
list = %jsonEncode @.nums
scalar = %jsonEncode @.text
```

**In**

```odin
text = "a\"b"

{data}
name = "John"
age = ##42

{nums[] : ~}
##1
##2
##3
```

**Out**

```odin
{out}
j = "{\"name\":\"John\",\"age\":42}"
list = "[1,2,3]"
scalar = "a\\\"b"
```

**Notes**

- Objects and arrays produce their full JSON serialization as a single string.
- For a scalar string, the result is the JSON-escaped body without the surrounding quotes.

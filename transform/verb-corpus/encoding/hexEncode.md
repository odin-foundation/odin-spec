# %hexEncode

Encode a string as hexadecimal

**Signature:** `%hexEncode <string> -> string`

Encode a string as a lowercase hexadecimal byte sequence.

**Transform**

```odin
{out}
h = %hexEncode @.data
```

**In**

```odin
data = "Hello"
```

**Out**

```odin
{out}
h = "48656c6c6f"
```

**Notes**

- Each byte is rendered as two lowercase hex digits.
- Characters above U+007F are encoded as their UTF-8 byte sequence.

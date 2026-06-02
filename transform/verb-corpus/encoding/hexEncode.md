# %hexEncode

Encode a string as hexadecimal

**Signature:** `%hexEncode <string> -> string`

Encode a string as a lowercase hexadecimal byte sequence.

**Transform**

```odin
{out}
h = %hexEncode @.data
world = %hexEncode @.other
empty = %hexEncode @.blank
```

**In**

```odin
data = "Hello"
other = "world"
blank = ""
```

**Out**

```odin
{out}
h = "48656c6c6f"
world = "776f726c64"
empty = ""
```

**Notes**

- Each byte is rendered as two lowercase hex digits.
- Characters above U+007F are encoded as their UTF-8 byte sequence.

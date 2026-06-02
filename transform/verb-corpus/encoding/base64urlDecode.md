# %base64urlDecode

decode URL-safe Base64

**Signature:** `%base64urlDecode <string> -> string`

Decode a URL-safe Base64 string (with or without padding) back to its UTF-8 text.

**Transform**

```odin
{out}
decoded = %base64urlDecode @.encoded
```

**In**

```odin
encoded = "aGVsbG8gd29ybGQ_Pj4"
```

**Out**

```odin
{out}
decoded = "hello world?>>"
```

**Notes**

- Accepts the URL-safe alphabet (- and _) and tolerates missing = padding.

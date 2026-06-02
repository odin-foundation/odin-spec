# %base64Encode

Base64-encode a string

**Signature:** `%base64Encode <string> -> string`

Encode a UTF-8 string as Base64 text.

**Transform**

```odin
{out}
encoded = %base64Encode @.content
```

**In**

```odin
content = "Hello, World!"
```

**Out**

```odin
{out}
encoded = "SGVsbG8sIFdvcmxkIQ=="
```

**Notes**

- The input is treated as UTF-8 before encoding, so non-ASCII text encodes its byte sequence.
- The result is a plain string, not an ODIN binary (^) value.

**Avoid**

- `encoded = %base64Encode content`: a bare word encodes the literal text "content", not the field; use @.content

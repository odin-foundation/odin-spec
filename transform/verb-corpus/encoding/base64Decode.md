# %base64Decode

Decode a Base64 string

**Signature:** `%base64Decode <string> -> string`

Decode Base64 text back into its original UTF-8 string.

**Transform**

```odin
{out}
dec = %base64Decode @.b
```

**In**

```odin
b = "SGVsbG8sIFdvcmxkIQ=="
```

**Out**

```odin
{out}
dec = "Hello, World!"
```

**Notes**

- Standard and URL-safe alphabets are both accepted (+/ or -_).
- Input that is not valid Base64 decodes to ~ (null) rather than raising an error.

**Avoid**

- `dec = %base64Decode @.b ; with b = "Hello!"`: a value that is not valid Base64 decodes to ~, not the original text

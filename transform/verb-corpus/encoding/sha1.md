# %sha1

SHA-1 hash of a string

**Signature:** `%sha1 <string> -> string`

Compute the SHA-1 digest of a string as lowercase hex.

**Transform**

```odin
{out}
h = %sha1 @.data
```

**In**

```odin
data = "hello"
```

**Out**

```odin
{out}
h = "aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d"
```

**Notes**

- The digest is deterministic and 40 lowercase hex characters long.
- SHA-1 is acceptable for content addressing but is deprecated for security.

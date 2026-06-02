# %sha1

SHA-1 hash of a string

**Signature:** `%sha1 <string> -> string`

Compute the SHA-1 digest of a string as lowercase hex.

**Transform**

```odin
{out}
h = %sha1 @.data
world = %sha1 @.other
empty = %sha1 @.blank
```

**In**

```odin
data = "hello"
other = "world"
blank = ""
```

**Out**

```odin
{out}
h = "aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d"
world = "7c211433f02071597741e6ff5a8ea34789abbf43"
empty = "da39a3ee5e6b4b0d3255bfef95601890afd80709"
```

**Notes**

- The digest is deterministic and 40 lowercase hex characters long.
- SHA-1 is acceptable for content addressing but is deprecated for security.

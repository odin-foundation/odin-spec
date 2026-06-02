# %md5

MD5 hash of a string

**Signature:** `%md5 <string> -> string`

Compute the MD5 digest of a string as lowercase hex.

**Transform**

```odin
{out}
h = %md5 @.data
```

**In**

```odin
data = "hello"
```

**Out**

```odin
{out}
h = "5d41402abc4b2a76b9719d911017c592"
```

**Notes**

- The digest is deterministic and 32 lowercase hex characters long.
- MD5 is suitable for checksums and cache keys only, not for security.

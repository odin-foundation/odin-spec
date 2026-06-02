# %md5

MD5 hash of a string

**Signature:** `%md5 <string> -> string`

Compute the MD5 digest of a string as lowercase hex.

**Transform**

```odin
{out}
h = %md5 @.data
world = %md5 @.other
empty = %md5 @.blank
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
h = "5d41402abc4b2a76b9719d911017c592"
world = "7d793037a0760186574b0282f2f435e7"
empty = "d41d8cd98f00b204e9800998ecf8427e"
```

**Notes**

- The digest is deterministic and 32 lowercase hex characters long.
- MD5 is suitable for checksums and cache keys only, not for security.

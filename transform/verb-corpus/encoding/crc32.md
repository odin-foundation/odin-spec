# %crc32

CRC-32 checksum of a string

**Signature:** `%crc32 <string> -> string`

Compute the CRC-32 (IEEE) checksum of a string as lowercase hex.

**Transform**

```odin
{out}
h = %crc32 @.data
check = %crc32 @.digits
empty = %crc32 @.blank
```

**In**

```odin
data = "hello"
digits = "123456789"
blank = ""
```

**Out**

```odin
{out}
h = "3610a686"
check = "cbf43926"
empty = "00000000"
```

**Notes**

- The checksum is deterministic and 8 lowercase hex characters long.
- CRC-32 is for data-integrity checks only, not cryptographic use.

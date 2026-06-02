# %crc32

CRC-32 checksum of a string

**Signature:** `%crc32 <string> -> string`

Compute the CRC-32 (IEEE) checksum of a string as lowercase hex.

**Transform**

```odin
{out}
h = %crc32 @.data
```

**In**

```odin
data = "hello"
```

**Out**

```odin
{out}
h = "3610a686"
```

**Notes**

- The checksum is deterministic and 8 lowercase hex characters long.
- CRC-32 is for data-integrity checks only, not cryptographic use.

# %hmac

keyed hash (HMAC)

**Signature:** `%hmac <message> <key> [<algorithm>] -> string`

Compute a hex HMAC of a message under a secret key; algorithm defaults to sha256.

**Transform**

```odin
{out}
mac = %hmac @.message @.key
```

**In**

```odin
message = "message"
key = "secret"
```

**Out**

```odin
{out}
mac = "8b5f48702995c1598c573db1e21866a9b825d4a794d169d7060a03605796360b"
```

**Notes**

- The digest is lowercase hex. The default algorithm is sha256; pass a third argument (e.g. "sha1", "sha512") to change it.
- The same message and key always produce the same digest.

**Avoid**

- `mac = %hmac @.message`: the key is required; with one argument the result is ~

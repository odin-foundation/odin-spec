# %hexDecode

Decode a hexadecimal string

**Signature:** `%hexDecode <string> -> string`

Decode a hexadecimal string back into its original text.

**Transform**

```odin
{out}
d = %hexDecode @.h
```

**In**

```odin
h = "48656C6C6F"
```

**Out**

```odin
{out}
d = "Hello"
```

**Notes**

- Upper- and lower-case hex digits are both accepted.
- An odd-length or non-hex string decodes to ~ (null) rather than raising an error.

**Avoid**

- `d = %hexDecode @.h ; with h = "48656"`: an odd number of hex digits is invalid and yields ~

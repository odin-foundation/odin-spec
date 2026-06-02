# %urlDecode

Decode a percent-encoded string

**Signature:** `%urlDecode <string> -> string`

Reverse percent-encoding, turning a URL component back into plain text.

**Transform**

```odin
{out}
dec = %urlDecode @.q
```

**In**

```odin
q = "a%20b%26c%3Dd"
```

**Out**

```odin
{out}
dec = "a b&c=d"
```

**Notes**

- Inverse of %urlEncode: %20 becomes a space, %26 an ampersand, and so on.
- A malformed escape (such as %zz) decodes to ~ (null) rather than raising an error.

**Avoid**

- `dec = %urlDecode @.q ; with q = "%zz"`: an invalid percent escape yields ~, not partial output

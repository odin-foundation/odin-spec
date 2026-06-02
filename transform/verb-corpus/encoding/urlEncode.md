# %urlEncode

Percent-encode a string

**Signature:** `%urlEncode <string> -> string`

Percent-encode a string so it is safe inside a URL component.

**Transform**

```odin
{out}
enc = %urlEncode @.q
```

**In**

```odin
q = "a b&c=d"
```

**Out**

```odin
{out}
enc = "a%20b%26c%3Dd"
```

**Notes**

- Encodes a single URI component: spaces become %20 and reserved characters such as & and = are escaped.
- Unreserved characters (letters, digits, - _ . ! ~ * ' ( )) pass through unchanged.

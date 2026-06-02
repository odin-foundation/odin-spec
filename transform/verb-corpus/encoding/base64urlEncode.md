# %base64urlEncode

URL-safe Base64 without padding

**Signature:** `%base64urlEncode <string> -> string`

Encode a UTF-8 string as URL-safe Base64 (- and _ instead of + and /, no = padding).

**Transform**

```odin
{out}
encoded = %base64urlEncode @.content
roundTrip = %base64urlDecode %base64urlEncode @.content
```

**In**

```odin
content = "hello world?>>"
```

**Out**

```odin
{out}
encoded = "aGVsbG8gd29ybGQ_Pj4"
roundTrip = "hello world?>>"
```

**Notes**

- The output uses the URL-safe alphabet and drops trailing = padding, so it is safe in query strings and path segments.
- Pair with %base64urlDecode to round-trip.

**Avoid**

- `encoded = %base64Encode @.content`: %base64Encode yields standard Base64 with +, / and = padding, which is not URL-safe

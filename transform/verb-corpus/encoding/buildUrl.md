# %buildUrl

assemble a URL from parts

**Signature:** `%buildUrl <object> -> string`

Assemble a URL from an object of scheme, host, port, path, query, and fragment.

**Transform**

```odin
{out}
url = %buildUrl @.parts
noScheme = %buildUrl @.bad
```

**In**

```odin
{parts}
scheme = "https"
host = "example.com"
port = ##8080
path = "/a/b"
{parts.query}
z = ##1
a = ##2
{parts}
fragment = "frag"

{bad}
host = "example.com"
```

**Out**

```odin
{out}
url = "https://example.com:8080/a/b?a=2&z=1#frag"
noScheme = ~
```

**Notes**

- The inverse of %parseUrl; query keys are sorted for canonical output.
- scheme and host are required; without them the result is ~.

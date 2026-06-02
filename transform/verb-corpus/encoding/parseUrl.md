# %parseUrl

split a URL into parts

**Signature:** `%parseUrl <string> -> object`

Parse a URL into an object of scheme, host, port, path, query, and fragment.

**Transform**

```odin
{out}
parts = %parseUrl @.url
```

**In**

```odin
url = "https://example.com:8080/a/b?z=1&a=2#frag"
```

**Out**

```odin
{out}
{.parts}
scheme = "https"
host = "example.com"
port = ##8080
path = "/a/b"
fragment = "frag"
{out.parts.query}
a = "2"
z = "1"
```

**Notes**

- Query keys are sorted for canonical output. The port is ~ when absent.
- Invert with %buildUrl.

# %parseUrl

split a URL into parts

**Signature:** `%parseUrl <string> -> object`

Parse a URL into an object of scheme, host, port, path, query, and fragment.

**Transform**

```odin
{out}
full = %parseUrl @.url
noPort = %parseUrl @.bare
invalid = %parseUrl @.bad
```

**In**

```odin
url = "https://example.com:8080/a/b?z=1&a=2#frag"
bare = "https://example.com/x"
bad = "not a url"
```

**Out**

```odin
{out}
invalid = ~
{.full}
scheme = "https"
host = "example.com"
port = ##8080
path = "/a/b"
fragment = "frag"
{out.full.query}
a = "2"
z = "1"
{.noPort}
scheme = "https"
host = "example.com"
port = ~
path = "/x"
fragment = ""
```

**Notes**

- Query keys are sorted for canonical output. The port is ~ when absent.
- Invert with %buildUrl.

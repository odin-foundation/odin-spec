# %parseQuery

parse a query string

**Signature:** `%parseQuery <string> -> object`

Parse a query string into an object; a leading ? is tolerated.

**Transform**

```odin
{out}
parsed = %parseQuery @.qs
leadingQ = %parseQuery @.q2
```

**In**

```odin
qs = "z=1&a=2"
q2 = "?a=2"
```

**Out**

```odin
{out}
leadingQ.a = "2"
{.parsed}
a = "2"
z = "1"
```

**Notes**

- Keys are sorted for canonical output. Values are strings.
- Invert with %buildQuery.

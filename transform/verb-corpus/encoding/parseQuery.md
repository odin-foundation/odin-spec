# %parseQuery

parse a query string

**Signature:** `%parseQuery <string> -> object`

Parse a query string into an object; a leading ? is tolerated.

**Transform**

```odin
{out}
q = %parseQuery @.qs
```

**In**

```odin
qs = "z=1&a=2"
```

**Out**

```odin
{out}
{.q}
a = "2"
z = "1"
```

**Notes**

- Keys are sorted for canonical output. Values are strings.
- Invert with %buildQuery.

# %jsonPath

Query a value with a JSONPath expression

**Signature:** `%jsonPath <value> <path:string> -> value`

Extract a nested value from an object using a JSONPath-style expression.

**Transform**

```odin
{out}
name = %jsonPath @.data "$.user.name"
```

**In**

```odin
data.user.name = "Ada"
data.user.id = ##7
```

**Out**

```odin
{out}
name = "Ada"
```

**Notes**

- Supports dot notation, [n] array indices, [*] wildcards, and ..field recursive descent.
- The path is a required second argument; a missing path yields ~ (null).

**Avoid**

- `name = %jsonPath @.data`: the path argument is required; without it the verb yields ~

# %jsonPath

Query a value with a JSONPath expression

**Signature:** `%jsonPath <value> <path:string> -> value`

Extract a nested value from an object using a JSONPath-style expression.

**Transform**

```odin
{out}
name = %jsonPath @.data "$.user.name"
first = %jsonPath @.data "$.items[0]"
missing = %jsonPath @.data "$.user.email"
```

**In**

```odin
{data.user}
name = "Ada"
id = ##7

{data.items[] : ~}
"x"
"y"
"z"
```

**Out**

```odin
{out}
name = "Ada"
first = "x"
missing = ~
```

**Notes**

- Supports dot notation, [n] array indices, [*] wildcards, and ..field recursive descent.
- The path is a required second argument; a missing path yields ~ (null).

**Avoid**

- `name = %jsonPath @.data`: the path argument is required; without it the verb yields ~

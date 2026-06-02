# %camelCase

convert to camelCase

**Signature:** `%camelCase <string> -> string`

Re-case a delimited or mixed-case string into camelCase.

**Transform**

```odin
{out}
out = %camelCase @.name
```

**In**

```odin
name = "hello-world"
```

**Out**

```odin
{out}
out = "helloWorld"
```

**Notes**

- Splits on hyphens, underscores, spaces, and case boundaries, then joins with the first word lowercased and the rest capitalized.

# %snakeCase

convert to snake_case

**Signature:** `%snakeCase <string> -> string`

Re-case a delimited or mixed-case string into snake_case.

**Transform**

```odin
{out}
out = %snakeCase @.name
```

**In**

```odin
name = "helloWorld"
```

**Out**

```odin
{out}
out = "hello_world"
```

**Notes**

- Splits on case boundaries and common delimiters, then joins the lowercased words with underscores.

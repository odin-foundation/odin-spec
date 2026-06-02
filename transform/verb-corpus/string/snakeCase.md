# %snakeCase

convert to snake_case

**Signature:** `%snakeCase <string> -> string`

Re-case a delimited or mixed-case string into snake_case.

**Transform**

```odin
{out}
out = %snakeCase @.name
pascal = %snakeCase @.pascal
kebab = %snakeCase @.kebab
```

**In**

```odin
name = "helloWorld"
pascal = "HelloWorld"
kebab = "hello-world-foo"
```

**Out**

```odin
{out}
out = "hello_world"
pascal = "hello_world"
kebab = "hello_world_foo"
```

**Notes**

- Splits on case boundaries and common delimiters, then joins the lowercased words with underscores.

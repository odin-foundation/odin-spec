# %kebabCase

convert to kebab-case

**Signature:** `%kebabCase <string> -> string`

Re-case a delimited or mixed-case string into kebab-case.

**Transform**

```odin
{out}
out = %kebabCase @.name
snake = %kebabCase @.snake
spaced = %kebabCase @.spaced
allCaps = %kebabCase @.allCaps
```

**In**

```odin
name = "helloWorld"
snake = "first_name"
spaced = "Two Words"
allCaps = "HTTPServer"
```

**Out**

```odin
{out}
out = "hello-world"
snake = "first-name"
spaced = "two-words"
allCaps = "http-server"
```

**Notes**

- Splits on case boundaries and common delimiters, then joins the lowercased words with hyphens.

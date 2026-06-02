# %pascalCase

convert to PascalCase

**Signature:** `%pascalCase <string> -> string`

Re-case a delimited or mixed-case string into PascalCase.

**Transform**

```odin
{out}
hyphen = %pascalCase @.name
spaced = %pascalCase @.spaced
snake = %pascalCase @.snake
```

**In**

```odin
name = "hello-world"
spaced = "hello world"
snake = "foo_bar_baz"
```

**Out**

```odin
{out}
hyphen = "HelloWorld"
spaced = "HelloWorld"
snake = "FooBarBaz"
```

**Notes**

- Splits on case boundaries and common delimiters, then joins the words with each first letter capitalized and no separator.

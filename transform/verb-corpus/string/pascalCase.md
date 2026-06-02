# %pascalCase

convert to PascalCase

**Signature:** `%pascalCase <string> -> string`

Re-case a delimited or mixed-case string into PascalCase.

**Transform**

```odin
{out}
out = %pascalCase @.name
```

**In**

```odin
name = "hello-world"
```

**Out**

```odin
{out}
out = "HelloWorld"
```

**Notes**

- Splits on case boundaries and common delimiters, then joins the words with each first letter capitalized and no separator.

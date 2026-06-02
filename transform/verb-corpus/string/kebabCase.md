# %kebabCase

convert to kebab-case

**Signature:** `%kebabCase <string> -> string`

Re-case a delimited or mixed-case string into kebab-case.

**Transform**

```odin
{out}
out = %kebabCase @.name
```

**In**

```odin
name = "helloWorld"
```

**Out**

```odin
{out}
out = "hello-world"
```

**Notes**

- Splits on case boundaries and common delimiters, then joins the lowercased words with hyphens.

# %mask

apply a formatting mask

**Signature:** `%mask <string> <pattern:string> -> string`

Lay a string's characters into a template, inserting literal mask punctuation.

**Transform**

```odin
{out}
out = %mask @.ssn "###-##-####"
```

**In**

```odin
ssn = "123456789"
```

**Out**

```odin
{out}
out = "123-45-6789"
```

**Notes**

- In the mask, the characters # A and * each consume one source character; any other character is emitted literally.
- Two required arguments: the source string and the mask pattern.

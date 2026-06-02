# %mask

apply a formatting mask

**Signature:** `%mask <string> <pattern:string> -> string`

Lay a string's characters into a template, inserting literal mask punctuation.

**Transform**

```odin
{out}
ssn = %mask @.ssn "###-##-####"
short = %mask @.short "###-##-####"
phone = %mask @.phone "(###) ###-####"
```

**In**

```odin
ssn = "123456789"
short = "12"
phone = "5551234567"
```

**Out**

```odin
{out}
ssn = "123-45-6789"
short = "12"
phone = "(555) 123-4567"
```

**Notes**

- In the mask, the characters # A and * each consume one source character; any other character is emitted literally.
- Two required arguments: the source string and the mask pattern.

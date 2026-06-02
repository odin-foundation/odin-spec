# %join

join an array into a string

**Signature:** `%join <array> <string:separator> -> string`

Concatenate array elements into one string with a separator.

**Transform**

```odin
{out}
tags = %join @.tags ", "
```

**In**

```odin
{tags[] : ~}
"insurance"
"auto"
"policy"
```

**Out**

```odin
{out}
tags = "insurance, auto, policy"
```

**Notes**

- The source array is written as an ODIN primitive-array block: {tags[] : ~} with one value per line.
- The separator is a required quoted literal.

**Avoid**

- `tags = %join @.tags`: the separator argument is required; omitting it raises a verb-arity error

# %join

join array elements with a delimiter

**Signature:** `%join <array> <delim:string> -> string`

Concatenate the elements of an array into a single string separated by a delimiter.

**Transform**

```odin
{out}
out = %join @.tags ", "
```

**In**

```odin
tags[0] = "a"
tags[1] = "b"
tags[2] = "c"
```

**Out**

```odin
{out}
out = "a, b, c"
```

**Notes**

- First argument is an array reference; second is the quoted delimiter.
- An array source is supplied in ODIN with indexed assignments (tags[0], tags[1], ...).

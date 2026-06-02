# %join

join array elements with a delimiter

**Signature:** `%join <array> <delim:string> -> string`

Concatenate the elements of an array into a single string separated by a delimiter.

**Transform**

```odin
{out}
out = %join @.tags ", "
pipe = %join @.tags "|"
single = %join @.one ", "
nums = %join @.nums "-"
```

**In**

```odin
tags[0] = "a"
tags[1] = "b"
tags[2] = "c"
one[0] = "solo"
nums[0] = ##1
nums[1] = ##2
```

**Out**

```odin
{out}
out = "a, b, c"
pipe = "a|b|c"
single = "solo"
nums = "1-2"
```

**Notes**

- First argument is an array reference; second is the quoted delimiter.
- An array source is supplied in ODIN with indexed assignments (tags[0], tags[1], ...).

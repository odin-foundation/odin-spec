# %reverseString

reverse the characters

**Signature:** `%reverseString <string> -> string`

Return the string with its characters in reverse order.

**Transform**

```odin
{out}
out = %reverseString @.name
```

**In**

```odin
name = "Honda"
```

**Out**

```odin
{out}
out = "adnoH"
```

**Notes**

- Single argument. Reversal is Unicode-aware (code points, not UTF-16 units).

# %reverseString

reverse the characters

**Signature:** `%reverseString <string> -> string`

Return the string with its characters in reverse order.

**Transform**

```odin
{out}
rev = %reverseString @.name
palindrome = %reverseString @.pal
single = %reverseString @.one
```

**In**

```odin
name = "Honda"
pal = "racecar"
one = "x"
```

**Out**

```odin
{out}
rev = "adnoH"
palindrome = "racecar"
single = "x"
```

**Notes**

- Single argument. Reverses the character order.

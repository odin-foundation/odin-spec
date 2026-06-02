# %concatArrays

join two arrays

**Signature:** `%concatArrays <array> <array> -> array`

Append the second array to the first, preserving order.

**Transform**

```odin
{out}
combined = %concatArrays @.a @.b
```

**In**

```odin
{a[] : ~}
"x"
"y"
{b[] : ~}
"z"
```

**Out**

```odin
{out}
{.combined[] : ~}
"x"
"y"
"z"
```

**Notes**

- Both arguments are arrays; the result is their concatenation.

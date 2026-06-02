# %concatArrays

join two arrays

**Signature:** `%concatArrays <array> <array> -> array`

Append the second array to the first, preserving order.

**Transform**

```odin
{out}
combined = %concatArrays @.a @.b
oneEmpty = %concatArrays @.a @.empty
bothNonArray = %concatArrays @.x @.y
```

**In**

```odin
x = "p"
y = "q"

{a[] : ~}
"x"
"y"

{b[] : ~}
"z"

{empty[] : ~}
```

**Out**

```odin
{out}
{.combined[] : ~}
"x"
"y"
"z"
{.oneEmpty[] : ~}
"x"
"y"
bothNonArray = ~
```

**Notes**

- Both arguments are arrays; the result is their concatenation.

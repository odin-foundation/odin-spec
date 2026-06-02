# %take

first N elements

**Signature:** `%take <array> <integer:count> -> array`

Return the first count elements of an array.

**Transform**

```odin
{out}
first2 = %take @.items ##2
```

**In**

```odin
{items[] : ~}
"a"
"b"
"c"
"d"
```

**Out**

```odin
{out}
{.first2[] : ~}
"a"
"b"
```

**Notes**

- A count larger than the array returns the whole array.

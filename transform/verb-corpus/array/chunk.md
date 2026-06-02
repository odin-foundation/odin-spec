# %chunk

split into fixed-size groups

**Signature:** `%chunk <array> <integer:size> -> array`

Break an array into sub-arrays of a given size.

**Transform**

```odin
{out}
pages = %chunk @.items ##2
zeroSize = %chunk @.items ##0
nonArray = %chunk @.notArr ##2
```

**In**

```odin
notArr = "x"

{items[] : ~}
##1
##2
##3
##4
##5
```

**Out**

```odin
{out}
{.pages[] : [0], [1]}
##1, ##2
##3, ##4
##5, 
zeroSize = ~
nonArray = ~
```

**Notes**

- Each chunk is a positional pair/tuple block; a trailing partial chunk keeps remaining elements.
- A size of zero or less returns null.

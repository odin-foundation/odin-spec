# %slice

sub-range of an array

**Signature:** `%slice <array> <integer:start> [<integer:end>] -> array`

Return elements from a start index up to (not including) an end index.

**Transform**

```odin
{out}
mid = %slice @.items ##1 ##3
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
{.mid[] : ~}
"b"
"c"
```

**Notes**

- End is exclusive: start ##1 end ##3 yields indices 1 and 2.
- Omitting end slices to the end of the array.

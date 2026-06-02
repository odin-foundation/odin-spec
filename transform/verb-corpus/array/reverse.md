# %reverse

reverse array order

**Signature:** `%reverse <array> -> array`

Return the array with element order reversed.

**Transform**

```odin
{out}
rev = %reverse @.items
```

**In**

```odin
{items[] : ~}
"a"
"b"
"c"
```

**Out**

```odin
{out}
{.rev[] : ~}
"c"
"b"
"a"
```

**Notes**

- Reverses in place order; the original array is not mutated.

# %reverse

reverse array order

**Signature:** `%reverse <array> -> array`

Return the array with element order reversed.

**Transform**

```odin
{out}
rev = %reverse @.items
single = %reverse @.one
```

**In**

```odin
{items[] : ~}
"a"
"b"
"c"

{one[] : ~}
"only"
```

**Out**

```odin
{out}
{.rev[] : ~}
"c"
"b"
"a"
{.single[] : ~}
"only"
```

**Notes**

- Reverses in place order; the original array is not mutated.

# %sample

random subset

**Signature:** `%sample <array> <integer:count> [<integer:seed>] -> array`

Draw N elements at random, optionally seeded for reproducibility.

**Transform**

```odin
{out}
picked = %sample @.items ##2 ##42
```

**In**

```odin
{items[] : ~}
"a"
"b"
"c"
"d"
"e"
```

**Out**

```odin
{out}
{.picked[] : ~}
"d"
"c"
```

**Notes**

- A seed makes the draw deterministic; the same seed always returns the same subset.
- Without a seed the result varies per run (seeded here for a stable example).
- Output depends on the TS seeded RNG sequence; verified in TS only.

# %zip

pair two arrays element-wise

**Signature:** `%zip <array> <array> -> array`

Combine two arrays into an array of positional pairs.

**Transform**

```odin
{out}
paired = %zip @.names @.scores
uneven = %zip @.names @.few
nonArray = %zip @.names @.scalar
```

**In**

```odin
scalar = "x"

{names[] : ~}
"alice"
"bob"

{scores[] : ~}
##90
##80

{few[] : ~}
##1
```

**Out**

```odin
{out}
{.paired[] : [0], [1]}
"alice", ##90
"bob", ##80
{.uneven[] : [0], [1]}
"alice", ##1
nonArray = ~
```

**Notes**

- Each result element is a [first, second] pair, emitted as a positional tabular block.
- Stops at the length of the shorter array.
- A non-array argument yields ~.

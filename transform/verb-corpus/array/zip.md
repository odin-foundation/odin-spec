# %zip

pair two arrays element-wise

**Signature:** `%zip <array> <array> -> array`

Combine two arrays into an array of positional pairs.

**Transform**

```odin
{out}
paired = %zip @.names @.scores
```

**In**

```odin
{names[] : ~}
"alice"
"bob"
{scores[] : ~}
##90
##80
```

**Out**

```odin
{out}
{.paired[] : [0], [1]}
"alice", ##90
"bob", ##80
```

**Notes**

- Each result element is a [first, second] pair, emitted as a positional tabular block.
- Stops at the length of the shorter array.

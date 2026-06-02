# %rank

assign ranks to values

**Signature:** `%rank <array> [<string:field>] [<string:direction>] -> array`

Tag each element with a _rank field (ties share a rank).

**Transform**

```odin
{out}
ranked = %rank @.scores
```

**In**

```odin
scores = "[10, 30, 20]"
```

**Out**

```odin
{out}
{.ranked[] : _rank, value}
##3, ##10
##1, ##30
##2, ##20
```

**Notes**

- Default direction is descending, so the largest value is rank 1.
- The array is supplied as a JSON-array string so the added _rank field surfaces in the output.

**Avoid**

- `{scores[] : ~} ##10 ##30 ##20`: a typed ODIN array block wraps each element, so the added _rank field is dropped from the serialized output; supply the array as a JSON-array string literal instead

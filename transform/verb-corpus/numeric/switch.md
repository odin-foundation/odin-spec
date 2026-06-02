# %switch

multi-way value mapping

**Signature:** `%switch <value> <case> <result> ... [default] -> any`

Map an input to a result by matching value/result pairs, with a trailing default.

**Transform**

```odin
{out}
v = %switch @.a "1" "one" "2" "two" "other"
```

**In**

```odin
a = "2"
```

**Out**

```odin
{out}
v = "two"
```

**Notes**

- Arguments after the value are case/result pairs; an odd trailing argument is the default.
- Matching is done on the string form of the value.

**Avoid**

- `v = %switch @.a`: at least one case/result pair (two args after the value) is required; the value alone raises a verb-arity error

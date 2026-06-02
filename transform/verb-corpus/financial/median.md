# %median

middle value

**Signature:** `%median <array:values> -> number`

Return the median of a numeric array, averaging the two middle values when the count is even.

**Transform**

```odin
{out}
r = %median @.prices
even = %median @.evenSet
single = %median @.one
```

**In**

```odin
{prices[] : ~}
#19.5
#21.0
#20.25
#22.75
#18.0

{evenSet[] : ~}
##10
##20
##30
##40

{one[] : ~}
##99
```

**Out**

```odin
{out}
r = #20.25
even = ##25
single = ##99
```

**Notes**

- The array is sorted internally; input order does not matter.
- For an even number of elements the result is the mean of the two central values.

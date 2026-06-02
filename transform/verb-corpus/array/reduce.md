# %reduce

fold an array with a verb

**Signature:** `%reduce <array> <string:verb> <value:initial> -> value`

Combine all elements into one value using a named verb and a seed.

**Transform**

```odin
{out}
total = %reduce @.prices "add" ##0
product = %reduce @.factors "multiply" ##1
seeded = %reduce @.prices "add" ##100
```

**In**

```odin
{prices[] : ~}
##10
##20
##30

{factors[] : ~}
##2
##3
##4
```

**Out**

```odin
{out}
total = ##60
product = ##24
seeded = ##160
```

**Notes**

- The verb name is a quoted string; it is applied as verb(accumulator, element).
- The third argument is the initial accumulator and the result for an empty array.

**Avoid**

- `total = %reduce @.prices %add ##0`: the fold verb must be a quoted name ("add"), not a nested %add call; the %add form is parsed as its own verb and raises an arity error

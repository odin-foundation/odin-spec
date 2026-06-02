# %range

generate a numeric sequence

**Signature:** `%range <integer:start> <integer:end> [<integer:step>] -> array`

Produce an array of integers from start to end (exclusive).

**Transform**

```odin
{out}
nums = %range ##1 ##5
```

**In**

```odin
{doc}
x = "y"
```

**Out**

```odin
{out}
{.nums[] : ~}
##1
##2
##3
##4
```

**Notes**

- End is exclusive: range ##1 ##5 yields 1,2,3,4.
- Takes literal numeric arguments rather than a source array; an optional third argument is the step.

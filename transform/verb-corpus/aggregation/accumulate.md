# %accumulate

running total across loop records

**Signature:** `%accumulate <name> <number> -> number`

Add a value to a named accumulator on each loop pass, building a running total.

**Transform**

```odin
{$accumulator}
total = ##0
total._persist = true
count = ##0
count._persist = true

{lines[]}
_loop = "@items"
amount = @.amount
_ = %accumulate total @.amount
_count = %accumulate count ##1

{summary}
total = "@$accumulator.total"
count = "@$accumulator.count"
```

**In**

```odin
{}
{items[] : amount}
##10
##20
##30
```

**Out**

```odin
{lines[] : amount}
##10
##20
##30
{summary}
total = ##60
count = ##3
```

**Notes**

- The accumulator must be declared in {$accumulator} with _persist = true so it survives across loop records; the first argument names it.
- Each loop pass adds the second argument to the current value; read the result back with @$accumulator.<name>.
- Each call runs as a side effect sunk into a _-prefixed field (not emitted); several such fields can coexist in one pass, so this updates total and count together.

**Avoid**

- `_ = %accumulate runningTotal @.amount (no {$accumulator} block declaring runningTotal)`: an undeclared accumulator makes %accumulate return ~ on every pass, so the total reads as ~ instead of the sum

# %accumulate

running total across loop records

**Signature:** `%accumulate <name> <number> -> number`

Add a value to a named accumulator on each loop pass, building a running total.

**Transform**

```odin
{$accumulator}
runningTotal = ##0
runningTotal._persist = true

{lines[]}
_loop = "@items"
amount = @.amount
_ = %accumulate runningTotal @.amount

{summary}
total = "@$accumulator.runningTotal"
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
summary.total = ##60
{lines[] : amount}
##10
##20
##30
```

**Notes**

- The accumulator must be declared in {$accumulator} with _persist = true so it survives across loop records; the first argument names it.
- Each loop pass adds the second argument to the current value; read the result back with @$accumulator.<name>.
- The verb runs as a side effect, conventionally sunk into a throwaway field (_); its return value is the new running total.

**Avoid**

- `_ = %accumulate runningTotal @.amount (no {$accumulator} block declaring runningTotal)`: an undeclared accumulator makes %accumulate return ~ on every pass, so the total reads as ~ instead of the sum

# %set

overwrite an accumulator with a value

**Signature:** `%set <name> <value> -> value`

Replace a named accumulator's current value on each loop pass, ending with the last record's value.

**Transform**

```odin
{$accumulator}
lastSeen = ##0
lastSeen._persist = true

{lines[]}
_loop = "@items"
val = @.v
_ = %set lastSeen @.v

{summary}
final = "@$accumulator.lastSeen"
```

**In**

```odin
{}
{items[] : v}
##10
##20
##30
```

**Out**

```odin
summary.final = ##30
{lines[] : val}
##10
##20
##30
```

**Notes**

- Unlike %accumulate, %set replaces rather than adds; after a loop the accumulator holds the last record's value.
- Supports any value type (string or number), not just numerics.
- The accumulator must be declared in {$accumulator} with _persist = true; the first argument names it.

**Avoid**

- `_ = %set lastSeen @.v (no {$accumulator} block declaring lastSeen)`: %set is a no-op on an undeclared accumulator and returns ~, so the read-back yields ~ instead of the value

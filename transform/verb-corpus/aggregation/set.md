# %set

overwrite an accumulator with a value

**Signature:** `%set <name> <value> -> value`

Replace a named accumulator's current value on each loop pass, ending with the last record's value.

**Transform**

```odin
{$accumulator}
lastVal = ##0
lastVal._persist = true
lastLabel = ""
lastLabel._persist = true

{lines[]}
_loop = "@items"
val = @.v
_ = %set lastVal @.v
_label = %set lastLabel @.label

{summary}
finalVal = "@$accumulator.lastVal"
finalLabel = "@$accumulator.lastLabel"
```

**In**

```odin
{}
{items[] : v, label}
##10, "a"
##20, "b"
##30, "c"
```

**Out**

```odin
{lines[] : val}
##10
##20
##30
{summary}
finalVal = ##30
finalLabel = "c"
```

**Notes**

- Unlike %accumulate, %set replaces rather than adds; after a loop the accumulator holds the last record's value.
- Supports any value type (string or number), not just numerics.
- The accumulator must be declared in {$accumulator} with _persist = true; the first argument names it.
- Sink the call into a _-prefixed field (not emitted); multiple such fields run in one pass, here advancing both lastVal and lastLabel.

**Avoid**

- `_ = %set lastSeen @.v (no {$accumulator} block declaring lastSeen)`: %set is a no-op on an undeclared accumulator and returns ~, so the read-back yields ~ instead of the value

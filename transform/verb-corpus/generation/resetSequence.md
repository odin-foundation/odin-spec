# %resetSequence

reset a named counter

**Signature:** `%resetSequence <string:name> -> integer`

Reset a named counter so a later %sequence on that name restarts.

**Transform**

```odin
{out}
resetTo = %resetSequence "ctr"
```

**In**

```odin
x = "a"
```

**Out**

```odin
{out}
resetTo = ##0
```

**Notes**

- The single argument is the quoted counter name; resetting returns ##0, and the next %sequence on that name yields ##1.
- Counter state is scoped to one transform execution.
- Deterministic, so it is corpus-safe.

**Avoid**

- `r = %resetSequence "ctr" ##5`: an inline reset value is rejected - a literal after the name raises a parse error (Unexpected content after value); the reset is always to 0

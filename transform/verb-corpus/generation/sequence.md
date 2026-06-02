# %sequence

next value in a named counter

**Signature:** `%sequence <string:name> -> integer`

Emit successive integers from a named counter that persists across mappings in one execution.

**Transform**

```odin
{out}
first = %sequence "ln"
second = %sequence "ln"
third = %sequence "ln"
```

**In**

```odin
x = "a"
```

**Out**

```odin
{out}
first = ##1
second = ##2
third = ##3
```

**Notes**

- The single argument is the quoted counter name; the first reference yields ##1 and each later reference to the same name increments.
- Counter state is scoped to one transform execution.
- Deterministic, so it is corpus-safe (unlike %uuid or %nanoid).

**Avoid**

- `n = %sequence "ln" ##100`: an inline start value is rejected - a literal after the name raises a parse error (Unexpected content after value); %sequence takes only the name and always begins at 1

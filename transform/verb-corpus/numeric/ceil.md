# %ceil

round up to integer

**Signature:** `%ceil <number> -> integer`

Round a number up toward positive infinity, returning an integer.

**Transform**

```odin
{out}
v = %ceil @.a
```

**In**

```odin
a = #3.2
```

**Out**

```odin
{out}
v = ##4
```

**Notes**

- Always returns an integer (##), even for a fractional input.
- Rounds toward positive infinity, so -3.7 would ceil to -3.

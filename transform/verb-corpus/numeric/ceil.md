# %ceil

round up to integer

**Signature:** `%ceil <number> -> integer`

Round a number up toward positive infinity, returning an integer.

**Transform**

```odin
{out}
v = %ceil @.a
negative = %ceil @.neg
already = %ceil @.whole
ofNull = %ceil @.nul
```

**In**

```odin
a = #3.2
neg = #-3.7
whole = ##5
nul = ~
```

**Out**

```odin
{out}
v = ##4
negative = ##-3
already = ##5
ofNull = ##0
```

**Notes**

- Always returns an integer (##), even for a fractional input.
- Rounds toward positive infinity, so -3.7 would ceil to -3.

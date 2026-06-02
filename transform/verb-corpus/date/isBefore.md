# %isBefore

chronological less-than

**Signature:** `%isBefore <date> <date> -> boolean`

Test whether the first date precedes the second.

**Transform**

```odin
{out}
b = %isBefore @.a @.b
```

**In**

```odin
a = 2024-01-01
b = 2024-06-15
```

**Out**

```odin
{out}
b = ?true
```

**Notes**

- Comparison is strict; equal dates yield false.
- Both arguments are interpreted in UTC.

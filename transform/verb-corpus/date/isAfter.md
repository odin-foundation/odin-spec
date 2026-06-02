# %isAfter

chronological greater-than

**Signature:** `%isAfter <date> <date> -> boolean`

Test whether the first date follows the second.

**Transform**

```odin
{out}
b = %isAfter @.a @.b
```

**In**

```odin
a = 2024-06-15
b = 2024-01-01
```

**Out**

```odin
{out}
b = ?true
```

**Notes**

- Comparison is strict; equal dates yield false.
- Both arguments are interpreted in UTC.

# %isBefore

chronological less-than

**Signature:** `%isBefore <date> <date> -> boolean`

Test whether the first date precedes the second.

**Transform**

```odin
{out}
before = %isBefore @.a @.b
equal = %isBefore @.b @.b
after = %isBefore @.b @.a
```

**In**

```odin
a = 2024-01-01
b = 2024-06-15
```

**Out**

```odin
{out}
before = ?true
equal = ?false
after = ?false
```

**Notes**

- Comparison is strict; equal dates yield false.
- Both arguments are interpreted in UTC.

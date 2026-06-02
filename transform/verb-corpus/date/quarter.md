# %quarter

calendar quarter of a date

**Signature:** `%quarter <date> -> integer`

Return the calendar quarter (1-4) that a date falls in.

**Transform**

```odin
{out}
n = %quarter @.date
```

**In**

```odin
date = 2024-06-15
```

**Out**

```odin
{out}
n = ##2
```

**Notes**

- Q1 is Jan-Mar, Q2 Apr-Jun, Q3 Jul-Sep, Q4 Oct-Dec.
- June falls in Q2.

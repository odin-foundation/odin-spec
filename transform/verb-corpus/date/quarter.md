# %quarter

calendar quarter of a date

**Signature:** `%quarter <date> -> integer`

Return the calendar quarter (1-4) that a date falls in.

**Transform**

```odin
{out}
q2 = %quarter @.date
q1 = %quarter @.jan
q4 = %quarter @.dec
```

**In**

```odin
date = 2024-06-15
jan = 2024-01-15
dec = 2024-12-31
```

**Out**

```odin
{out}
q2 = ##2
q1 = ##1
q4 = ##4
```

**Notes**

- Q1 is Jan-Mar, Q2 Apr-Jun, Q3 Jul-Sep, Q4 Oct-Dec.
- June falls in Q2.

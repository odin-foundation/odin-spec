# %daysBetweenDates

whole days from start to end

**Signature:** `%daysBetweenDates <date:start> <date:end> -> integer`

Count the whole days between two dates, signed by direction.

**Transform**

```odin
{out}
n = %daysBetweenDates @.a @.b
```

**In**

```odin
a = 2024-01-01
b = 2024-01-15
```

**Out**

```odin
{out}
n = ##14
```

**Notes**

- Result is end minus start; an earlier end yields a negative count.
- A focused alternative to %dateDiff with the "days" unit.

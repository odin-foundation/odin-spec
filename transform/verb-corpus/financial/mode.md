# %mode

most frequent value

**Signature:** `%mode <array:values> -> number`

Return the most frequently occurring value in a numeric array.

**Transform**

```odin
{out}
r = %mode @.values
```

**In**

```odin
{values[] : ~}
##3
##5
##3
##7
##3
```

**Out**

```odin
{out}
r = ##3
```

**Notes**

- 3 appears three times, more than any other value.
- On a tie the first value to reach the highest count wins.

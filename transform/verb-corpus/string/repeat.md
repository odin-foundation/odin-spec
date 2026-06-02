# %repeat

repeat a string N times

**Signature:** `%repeat <string> <count:integer> -> string`

Concatenate a string with itself a given number of times.

**Transform**

```odin
{out}
thrice = %repeat @.ab ##3
zero = %repeat @.ab ##0
negative = %repeat @.ab ##-1
```

**In**

```odin
ab = "ab"
```

**Out**

```odin
{out}
thrice = "ababab"
zero = ""
negative = ~
```

**Notes**

- Two required arguments: the string and a non-negative repeat count.
- A count of 0 returns an empty string; a negative count returns null. The total length is capped by an internal limit.

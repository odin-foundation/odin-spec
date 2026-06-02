# %soundex

phonetic code

**Signature:** `%soundex <string> -> string`

Return the 4-character Soundex code for a word.

**Transform**

```odin
{out}
out = %soundex @.name
similar = %soundex @.rupert
short = %soundex @.lee
```

**In**

```odin
name = "Robert"
rupert = "Rupert"
lee = "Lee"
```

**Out**

```odin
{out}
out = "R163"
similar = "R163"
short = "L000"
```

**Notes**

- Single argument. Keeps the first letter, encodes the rest, and pads to four characters.
- Similar-sounding names share a code (Robert and Rupert both yield R163).

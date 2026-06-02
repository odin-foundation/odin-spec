# %soundex

phonetic code

**Signature:** `%soundex <string> -> string`

Return the 4-character Soundex code for a word.

**Transform**

```odin
{out}
out = %soundex @.name
```

**In**

```odin
name = "Robert"
```

**Out**

```odin
{out}
out = "R163"
```

**Notes**

- Single argument. Keeps the first letter, encodes the rest, and pads to four characters.
- Similar-sounding names share a code (Robert and Rupert both yield R163).

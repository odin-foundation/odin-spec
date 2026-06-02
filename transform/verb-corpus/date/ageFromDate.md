# %ageFromDate

completed years between two dates

**Signature:** `%ageFromDate <date:birth> <date:asOf> -> integer`

Compute age in complete years from a birth date as of a reference date.

**Transform**

```odin
{out}
n = %ageFromDate @.birth @.asOf
beforeBirthday = %ageFromDate @.birthLate @.asOf
onBirthday = %ageFromDate @.birthExact @.asOf
bad = %ageFromDate @.notDate @.asOf
```

**In**

```odin
birth = 1990-06-15
birthLate = 1990-12-25
birthExact = 2000-06-15
asOf = 2024-06-15
notDate = "nope"
```

**Out**

```odin
{out}
n = ##34
beforeBirthday = ##33
onBirthday = ##24
bad = ~
```

**Notes**

- Only complete years are counted; a birthday not yet reached this year is excluded.
- Provide the as-of date explicitly for a deterministic result; omitting it uses the current date.

**Avoid**

- `n = %ageFromDate @.birth`: without an as-of date the result depends on the current clock and is not reproducible

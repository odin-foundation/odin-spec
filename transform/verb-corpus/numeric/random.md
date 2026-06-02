# %random

seeded random number in range

**Signature:** `%random <number:min> <number:max> [string:seed] -> integer`

Generate a deterministic random integer within an inclusive range using a string seed.

**Transform**

```odin
{out}
v = %random ##1 ##10 "seed123"
```

**In**

```odin
x = #0
```

**Out**

```odin
{out}
v = ##2
```

**Notes**

- With min and max supplied, the result is an integer in the inclusive range [min, max].
- A string seed makes the result deterministic; the same seed always yields the same value.
- Without a seed the result is non-deterministic, so corpus usage should always pass a seed.

**Avoid**

- `v = %random ##1 ##10`: without a seed the generator is non-deterministic; supply a string seed for reproducible output

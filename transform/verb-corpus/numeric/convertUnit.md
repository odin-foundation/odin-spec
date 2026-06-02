# %convertUnit

unit conversion

**Signature:** `%convertUnit <number> <string:fromUnit> <string:toUnit> -> number`

Convert a value between two compatible units of the same family.

**Transform**

```odin
{out}
v = %convertUnit @.a "kg" "lb"
temp = %convertUnit @.t "C" "F"
crossFamily = %convertUnit @.a "kg" "km"
unknownUnit = %convertUnit @.a "kg" "zz"
```

**In**

```odin
a = #100
t = #100
```

**Out**

```odin
{out}
v = #220.462442
temp = ##212
crossFamily = ~
unknownUnit = ~
```

**Notes**

- Supports mass, length, volume, speed, area, data, time, and temperature (C/F/K) families.
- Results are rounded to at most six decimal places.
- Incompatible units across families (e.g. kg -> km) yield null (~).

**Avoid**

- `v = %convertUnit @.a "kg" "km"`: the units belong to different families (mass vs length), so the conversion yields null (~)

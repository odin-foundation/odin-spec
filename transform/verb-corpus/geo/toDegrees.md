# %toDegrees

radians to degrees

**Signature:** `%toDegrees <number:radians> -> number`

Convert an angle in radians to degrees.

**Transform**

```odin
{out}
deg = %toDegrees @.rad
zero = %toDegrees @.zero
negative = %toDegrees @.negRad
```

**In**

```odin
rad = #3.141592653589793
zero = #0
negRad = #-1.5707963267948966
```

**Out**

```odin
{out}
deg = #180
zero = #0
negative = #-90
```

**Notes**

- Single numeric argument in radians; pi radians maps to 180 degrees.
- The result is a number (#).

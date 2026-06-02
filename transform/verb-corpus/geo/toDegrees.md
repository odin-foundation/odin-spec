# %toDegrees

radians to degrees

**Signature:** `%toDegrees <number:radians> -> number`

Convert an angle in radians to degrees.

**Transform**

```odin
{out}
deg = %toDegrees @.rad
```

**In**

```odin
rad = #3.141592653589793
```

**Out**

```odin
{out}
deg = #180
```

**Notes**

- Single numeric argument in radians; pi radians maps to 180 degrees.
- The result is a number (#).

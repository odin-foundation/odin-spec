# %toRadians

degrees to radians

**Signature:** `%toRadians <number:degrees> -> number`

Convert an angle in degrees to radians.

**Transform**

```odin
{out}
rad = %toRadians @.deg
```

**In**

```odin
deg = #180
```

**Out**

```odin
{out}
rad = #3.141592653589793
```

**Notes**

- Single numeric argument in degrees; 180 degrees maps to pi radians.
- The result is a number (#).

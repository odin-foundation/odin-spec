# %toRadians

degrees to radians

**Signature:** `%toRadians <number:degrees> -> number`

Convert an angle in degrees to radians.

**Transform**

```odin
{out}
rad = %toRadians @.deg
zero = %toRadians @.zero
fullTurn = %toRadians @.full
```

**In**

```odin
deg = #180
zero = #0
full = #360
```

**Out**

```odin
{out}
rad = #3.141592653589793
zero = #0
fullTurn = #6.283185307179586
```

**Notes**

- Single numeric argument in degrees; 180 degrees maps to pi radians.
- The result is a number (#).

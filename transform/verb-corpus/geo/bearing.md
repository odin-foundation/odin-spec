# %bearing

initial heading between two points

**Signature:** `%bearing <number:lat1> <number:lng1> <number:lat2> <number:lng2> -> number`

Compute the initial bearing (forward azimuth) in degrees from one point to another.

**Transform**

```odin
{out}
heading = %bearing @.lat1 @.lng1 @.lat2 @.lng2
samePoint = %bearing @.lat1 @.lng1 @.lat1 @.lng1
outOfRange = %bearing @.lat1 @.lng1 @.badLat @.lng2
```

**In**

```odin
lat1 = #40.7128
lng1 = #-74.006
lat2 = #34.0522
lng2 = #-118.2437
badLat = #91
```

**Out**

```odin
{out}
heading = #273.6871323393308
samePoint = #0
outOfRange = ~
```

**Notes**

- Arguments are lat1, lng1, lat2, lng2; the result is degrees normalized to 0-360 (0 = North, 90 = East).
- Coordinates outside lat +/-90 or lng +/-180 resolve to ~.

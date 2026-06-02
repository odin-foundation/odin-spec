# %midpoint

geographic midpoint of two points

**Signature:** `%midpoint <number:lat1> <number:lng1> <number:lat2> <number:lng2> -> object`

Compute the great-circle midpoint between two lat/lng coordinates.

**Transform**

```odin
{out}
mid = %midpoint @.lat1 @.lng1 @.lat2 @.lng2
same = %midpoint @.lat1 @.lng1 @.lat1 @.lng1
outOfRange = %midpoint @.lat1 @.lng1 @.badLat @.lng2
```

**In**

```odin
lat1 = #40.7128
lng1 = #-74.006
lat2 = #34.0522
lng2 = #-118.2437
badLat = #200
```

**Out**

```odin
{out}
outOfRange = ~
{.mid}
lat = #39.510307565757
lon = #-97.16013188872247
{.same}
lat = #40.7128
lon = #-74.006
```

**Notes**

- Arguments are lat1, lng1, lat2, lng2; the result is an object with lat and lon members, serialized as a {.mid} sub-section.
- The object keys are lat and lon (not lng).

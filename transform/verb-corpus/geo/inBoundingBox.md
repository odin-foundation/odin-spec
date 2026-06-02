# %inBoundingBox

test a point against a rectangle

**Signature:** `%inBoundingBox <number:lat> <number:lng> <number:minLat> <number:minLng> <number:maxLat> <number:maxLng> -> boolean`

Return true when a lat/lng point falls inside a min/max bounding box.

**Transform**

```odin
{out}
inside = %inBoundingBox @.lat @.lng #24.5 #-125 #49.5 #-66
```

**In**

```odin
lat = #40.7128
lng = #-74.006
```

**Out**

```odin
{out}
inside = ?true
```

**Notes**

- Argument order is lat, lng, minLat, minLng, maxLat, maxLng; the result is a boolean (?).
- Bounds are inclusive on every edge.

**Avoid**

- `inside = %inBoundingBox @.lat @.lng ##24.5 #-125 #49.5 #-66`: fractional bounds must use the number prefix (#); ##24.5 raises a parse error because an integer (##) cannot have a fractional part

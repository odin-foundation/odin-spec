# %distance

great-circle distance between two points

**Signature:** `%distance <number:lat1> <number:lng1> <number:lat2> <number:lng2> [<string:unit>] -> number`

Compute the Haversine distance between two lat/lng coordinates in kilometers.

**Transform**

```odin
{out}
km = %distance @.lat1 @.lng1 @.lat2 @.lng2
```

**In**

```odin
lat1 = #40.7128
lng1 = #-74.006
lat2 = #34.0522
lng2 = #-118.2437
```

**Out**

```odin
{out}
km = #3935.746254609722
```

**Notes**

- Arguments are lat1, lng1, lat2, lng2; the result is a number (#) in kilometers by default.
- Pass a fifth "miles" or "mi" argument for miles.
- Coordinates outside lat +/-90 or lng +/-180 resolve to ~.
- Output is a TS shortest-round-trip float (last-digit f64 differs cross-language); verified in TS only.

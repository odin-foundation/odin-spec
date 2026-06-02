# %movingAvg

rolling window average

**Signature:** `%movingAvg <array:values> <integer:window> -> array`

Smooth a numeric series with a simple moving average over a fixed window.

**Transform**

```odin
{out}
r = %movingAvg @.prices @.w
windowOne = %movingAvg @.prices @.one
wideWindow = %movingAvg @.prices @.big
```

**In**

```odin
{prices[] : ~}
##10
##20
##30
##40
##50

w = ##3
one = ##1
big = ##10
```

**Out**

```odin
{out}
{.r[] : ~}
##10
##15
##20
##30
##40
{.windowOne[] : ~}
##10
##20
##30
##40
##50
{.wideWindow[] : ~}
##10
##15
##20
##25
##30
```

**Notes**

- Returns an array the same length as the input; leading positions use a partial window of the data available so far.
- The result is emitted as an array block ({.r[] : ~}) since each position has its own average.
- A window < 1 yields ~ (null).

**Avoid**

- `r = %movingAvg @.prices @.w ; w = ##0`: a window size below 1 yields ~ (null); the window must be a positive integer

# %safeDivide

division with zero fallback

**Signature:** `%safeDivide <number:numerator> <number:denominator> <default> -> number`

Divide two numbers, returning a supplied default when the divisor is zero or invalid.

**Transform**

```odin
{out}
v = %safeDivide @.a @.b ##0
```

**In**

```odin
a = #10
b = #0
```

**Out**

```odin
{out}
v = ##0
```

**Notes**

- When the denominator is 0, NaN, or Infinity, the third argument (the default) is returned as-is.
- Avoids the verbose %ifElse %eq ... %divide pattern.

**Avoid**

- `v = %safeDivide @.a @.b`: the default value is a required third argument; omitting it raises a verb-arity error

# %gt

greater than

**Signature:** `%gt <a:value> <b:value> -> boolean`

Return true when the first value is strictly greater than the second.

**Transform**

```odin
{out}
big = %gt @.amount ##1000
```

**In**

```odin
amount = ##2500
```

**Out**

```odin
{out}
big = ?true
```

**Notes**

- Numeric operands compare numerically; two strings compare lexically.
- This is the canonical condition builder for %ifElse, %and, %or, and %assert.

**Avoid**

- `big = %gt "@.amount > 1000"`: %gt needs two separate operands; a single quoted infix string is just one argument, so the transform fails with a 'requires 2 arguments' error. Write %gt @.amount ##1000.

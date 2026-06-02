# %split

split and select one element

**Signature:** `%split <string> <delim:string> <index:integer> -> string`

Split a string on a delimiter and return the element at a given index.

**Transform**

```odin
{out}
out = %split @.csv "," ##1
```

**In**

```odin
csv = "a,b,c"
```

**Out**

```odin
{out}
out = "b"
```

**Notes**

- Three required arguments: string, delimiter, then the zero-based index to select.
- Negative indices count from the end. Out-of-range indices return null. Use %tokenize to get the whole array.

**Avoid**

- `out = %split @.csv ","`: omitting the index argument fails the 3-argument requirement and yields null (~); %split returns a single element, not the array

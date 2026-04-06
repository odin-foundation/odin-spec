# ODIN Transform 1.0: Verbs

<!-- LLM:
PURPOSE: Transformation verb reference with syntax and examples
STRING: concat upper lower capitalize titleCase trim substring replace split join padLeft padRight truncate mask
NUMERIC: formatNumber formatInteger formatCurrency abs round floor ceil multiply divide add subtract mod negate
DATE: formatDate formatTime formatTimestamp parseDate addDays addMonths addYears dateDiff today now
LOOKUP: lookup lookupDefault ("TABLE.column" @match1 @match2) — TABLE.column MUST be quoted, variadic arity
AGGREGATE: accumulate sum count min max avg first last
CONDITIONAL: coalesce ifElse ifNull ifEmpty switch
ARRAY: at filter flatten distinct sort sortBy reverse
COERCE: coerceString coerceNumber coerceInteger coerceBoolean coerceDate tryCoerce
STATISTICAL: std variance median mode percentile correlation
FINANCIAL: compound discount pmt fv pv
CUSTOM: %&namespace.verb for extensions
SEE ALSO: Core.md Modifiers.md Formats.md Reference.md
-->

---

## Verb Expression Syntax

**Unquoted (Preferred):**
```odin
full_name = %concat @firstName " " @lastName
status = %upper @.status
```

**Quoted (Legacy):**
```odin
full_name = "%concat @firstName \" \" @lastName"
```

**Nested Verbs:**
```odin
display_name = %upper %concat @first " " @last
```

**Nesting Constraint:** Variadic verbs (e.g., `%lookup`, `%concat`, `%coalesce`) cannot be nested as arguments inside fixed-arity verbs (e.g., `%multiply`, `%divide`). The parser cannot determine where the variadic arguments end. Break these into separate accumulator steps — see [Core: Verb Nesting Constraints](Core.md#verb-nesting-constraints).

---

## String Transformations

| Verb | Syntax | Description |
|------|--------|-------------|
| `concat` | `%concat @p1 "sep" @p2` | Concatenate values |
| `upper` | `%upper @path` | Uppercase |
| `lower` | `%lower @path` | Lowercase |
| `capitalize` | `%capitalize @path` | Capitalize first |
| `titleCase` | `%titleCase @path` | Title Case |
| `trim` | `%trim @path` | Remove whitespace |
| `trimLeft` | `%trimLeft @path` | Remove leading whitespace |
| `trimRight` | `%trimRight @path` | Remove trailing whitespace |
| `length` | `%length @path` | String length |
| `contains` | `%contains @path "search"` | Boolean: contains substring |
| `startsWith` | `%startsWith @path "prefix"` | Boolean: starts with |
| `endsWith` | `%endsWith @path "suffix"` | Boolean: ends with |
| `substring` | `%substring @path start len` | Extract substring (0-based) |
| `replace` | `%replace @path "find" "repl"` | Replace all occurrences |
| `replaceRegex` | `%replaceRegex @path "pat" "repl"` | Regex replacement |
| `padLeft` | `%padLeft @path len "char"` | Left-pad to length |
| `padRight` | `%padRight @path len "char"` | Right-pad to length |
| `truncate` | `%truncate @path len` | Truncate to max length |
| `mask` | `%mask @path "pattern"` | Apply mask (# = digit) |
| `split` | `%split @path "delim" index` | Split and take element |
| `join` | `%join @array "delim"` | Join array elements |
| `formatPhone` | `%formatPhone @value "countryCode"` | Format phone number |

**Examples:**

```odin
; concat
full_name = %concat @name.first " " @name.last      ; "John Smith"

; case conversion
state = %upper @address.state                        ; "tx" → "TX"
name = %capitalize @name.first                       ; "john" → "John"

; substring / replace
area_code = %substring @phone ##0 ##3               ; "5125551234" → "512"
no_dashes = %replace @ssn "-" ""                     ; "123-45-6789" → "123456789"

; pad / truncate
seq = %padLeft @sequence ##5 "0"                     ; "42" → "00042"
short = %truncate @description ##20                  ; Limit to 20 chars

; mask
phone = %mask @phone "###-###-####"                 ; "5125551234" → "512-555-1234"

; split / join
first_name = %split @full_name " " ##0              ; "John Smith" → "John"
phone_list = %join @phones ", "                      ; ["512...", "713..."] → "512..., 713..."
```

---

## Numeric Transformations

| Verb | Syntax | Description |
|------|--------|-------------|
| `formatNumber` | `%formatNumber @path decimals` | Format with decimal places |
| `formatInteger` | `%formatInteger @path` | Format as integer |
| `formatCurrency` | `%formatCurrency @path` | Format as currency (2 decimals) |
| `abs` | `%abs @path` | Absolute value |
| `round` | `%round @path decimals` | Round to decimal places |
| `floor` | `%floor @path` | Round down |
| `ceil` | `%ceil @path` | Round up |
| `multiply` | `%multiply @path factor` | Multiply |
| `divide` | `%divide @path divisor` | Divide |
| `add` | `%add @path1 @path2` | Add |
| `subtract` | `%subtract @path1 @path2` | Subtract |
| `mod` | `%mod @path divisor` | Modulo |
| `negate` | `%negate @path` | Negate |
| `convertUnit` | `%convertUnit @value "fromUnit" "toUnit"` | Convert between units |

**Examples:**

```odin
; formatting
premium = %formatNumber @premium ##2                 ; 1234.5 → "1234.50"
year = %formatInteger @vehicle.year                  ; 2024.0 → "2024"

; arithmetic
surcharge = %multiply @base_premium #1.15            ; 100 → 115 (15%)
monthly = %divide @annual_premium ##12               ; 1200 → 100
total = %add @base @surcharges                       ; 500 + 75 → 575
```

---

## Financial & Statistical Transformations

### Mathematical Functions

| Verb | Syntax | Description |
|------|--------|-------------|
| `log` | `%log @value [base]` | Logarithm |
| `ln` | `%ln @value` | Natural logarithm |
| `log10` | `%log10 @value` | Base-10 logarithm |
| `exp` | `%exp @value` | Exponential (e^x) |
| `pow` | `%pow @base @exponent` | Power function |
| `sqrt` | `%sqrt @value` | Square root |

### Time Value of Money

| Verb | Syntax | Description |
|------|--------|-------------|
| `compound` | `%compound @principal @rate @periods` | Future value: P × (1 + r)^n |
| `discount` | `%discount @futureValue @rate @periods` | Present value: FV / (1 + r)^n |
| `pmt` | `%pmt @principal @rate @periods` | Payment for loan |
| `fv` | `%fv @payment @rate @periods` | Future value of annuity |
| `pv` | `%pv @payment @rate @periods` | Present value of annuity |

### Statistics (Array-Based)

| Verb | Syntax | Description |
|------|--------|-------------|
| `std` | `%std @array.field` | Standard deviation (population) |
| `stdSample` | `%stdSample @array.field` | Standard deviation (sample) |
| `variance` | `%variance @array.field` | Variance (population) |
| `varianceSample` | `%varianceSample @array.field` | Variance (sample) |
| `median` | `%median @array.field` | Median value |
| `mode` | `%mode @array.field` | Most frequent value |
| `percentile` | `%percentile @array.field @pct` | Nth percentile (0-100) |
| `quantile` | `%quantile @array.field @q` | Quantile (0-1) |
| `covariance` | `%covariance @array1 @array2` | Covariance |
| `correlation` | `%correlation @array1 @array2` | Pearson correlation |

### Utility Functions

| Verb | Syntax | Description |
|------|--------|-------------|
| `clamp` | `%clamp @value @min @max` | Constrain to range |
| `interpolate` | `%interpolate @x @x1 @y1 @x2 @y2` | Linear interpolation |
| `weightedAvg` | `%weightedAvg @values @weights` | Weighted average |
| `movingAvg` | `%movingAvg @array ##windowSize` | Rolling window average |

**Examples:**

```odin
; time value of money
future_value = %compound ##10000 #0.05 ##10          ; 10000 × 1.05^10 → 16288.95
monthly_pmt = %pmt ##200000 %divide #0.06 ##12 ##360 ; Loan payment

; statistics
volatility = %std @claims.amount                     ; Standard deviation
median_premium = %median @policies.premium           ; Middle value
q3 = %percentile @values ##75                        ; 75th percentile

; utility
bounded = %clamp @calculated ##500 ##5000            ; Constrain to range
```

---

## Date/Time Transformations

| Verb | Syntax | Description |
|------|--------|-------------|
| `formatDate` | `%formatDate @path "pattern"` | Format date |
| `formatTime` | `%formatTime @path "pattern"` | Format time |
| `formatTimestamp` | `%formatTimestamp @path "pattern"` | Format timestamp |
| `parseDate` | `%parseDate @path "pattern"` | Parse date from string |
| `parseTimestamp` | `%parseTimestamp @path "pattern"` | Parse timestamp |
| `addDays` | `%addDays @path days` | Add days |
| `addMonths` | `%addMonths @path months` | Add months |
| `addYears` | `%addYears @path years` | Add years |
| `dateDiff` | `%dateDiff @path1 @path2 "unit"` | Difference in units |
| `today` | `%today` | Current date |
| `now` | `%now` | Current timestamp |
| `businessDays` | `%businessDays @date ##count` | Add N business days |
| `nextBusinessDay` | `%nextBusinessDay @date` | Next Mon-Fri date |
| `formatDuration` | `%formatDuration @duration` | Human-readable duration |

**Date Format Patterns:**

| Pattern | Description | Example |
|---------|-------------|---------|
| `YYYY` | 4-digit year | 2024 |
| `YY` | 2-digit year | 24 |
| `MM` | 2-digit month | 06 |
| `DD` | 2-digit day | 15 |
| `HH` | 2-digit hour (24h) | 14 |
| `hh` | 2-digit hour (12h) | 02 |
| `mm` | 2-digit minute | 30 |
| `ss` | 2-digit second | 45 |
| `A` | AM/PM | PM |

**Examples:**

```odin
; formatting
eff_us = %formatDate @effective "MMDDYYYY"           ; 2024-06-15 → "06152024"
eff_iso = %formatDate @effective "YYYY-MM-DD"        ; → "2024-06-15"
ts = %formatTimestamp @created "YYYYMMDDHHmmss"      ; → "20240615143045"

; parsing
parsed = %parseDate @date_string "MMDDYYYY"          ; "06152024" → date

; arithmetic
expiration = %addMonths @effective ##6               ; 2024-06-15 → 2024-12-15
days_remaining = %dateDiff @today @expiration "days" ; → 183

; current
process_date = %formatDate %today "YYYYMMDD"
```

---

## Lookup Transformations

| Verb | Syntax | Description |
|------|--------|-------------|
| `lookup` | `%lookup "TABLE.column" @match1 [@match2]` | Lookup returning column |
| `lookupDefault` | `%lookupDefault "TABLE.column" @match1 "default"` | With fallback |

**Important:** The `TABLE.column` argument **must be quoted**. Unquoted `TABLE.column` is tokenized as three separate tokens (`TABLE`, `.`, `column`) and produces a parse error.

**Arity:** `lookup` and `lookupDefault` are **variadic** — they accept a variable number of match arguments (one per key column). This means they cannot be nested inside fixed-arity verbs like `%multiply`. Use an accumulator step instead (see [Core: Verb Nesting Constraints](Core.md#verb-nesting-constraints)).

**Examples:**

```odin
; Single-column match — quote "TABLE.column"
body_code = %lookup "BODY_TYPES.code" @.bodyType       ; "sedan" → "01"

; Reverse lookup (same syntax)
body_name = %lookup "BODY_TYPES.name" @.bodyCode       ; "01" → "sedan"

; Multi-column match
territory = %lookup "TERRITORY.territory" @.state @.zipPrefix
; "TX", "787" → "AUSTIN"

; With default
code = %lookupDefault "BODY_TYPES.code" @.bodyType "99"
```

---

## Aggregation Transformations

| Verb | Syntax | Description |
|------|--------|-------------|
| `accumulate` | `%accumulate name value` | Add to accumulator |
| `sum` | `%sum @array.field` | Sum field values |
| `count` | `%count @array` | Count items |
| `min` | `%min @array.field` | Minimum |
| `max` | `%max @array.field` | Maximum |
| `avg` | `%avg @array.field` | Average |
| `first` | `%first @array` | First item |
| `last` | `%last @array` | Last item |

**Examples:**

```odin
; accumulator — use _ (sink field) to accumulate without output
{_step1}
_ = %accumulate record_count ##1

{_step2}
_ = %accumulate premium_total @coverage.premium

{summary}
total = @$accumulator.premium_total
```

**Note:** The `_` target field executes the verb but emits no output. Only one `_ =` per section (duplicate path P007 otherwise). Use separate `{_stepN}` sections for multiple accumulations. See [Core: The `_` Sink Field](Core.md#the-_-sink-field).

```odin
; array aggregation
total_premium = %sum @vehicles.premium
vehicle_count = %count @vehicles
avg_premium = %avg @vehicles.premium
oldest_year = %min @vehicles.year
primary_vin = %first @vehicles.vin
```

---

## Conditional Transformations

| Verb | Syntax | Description |
|------|--------|-------------|
| `coalesce` | `%coalesce @p1 @p2 @p3` | First non-null |
| `ifElse` | `%ifElse @condition @then @else` | Conditional |
| `ifNull` | `%ifNull @path @fallback` | Fallback if null |
| `ifEmpty` | `%ifEmpty @path @fallback` | Fallback if empty string |
| `switch` | `%switch @path "v1" "r1" "v2" "r2" "default"` | Multi-way |

**Examples:**

```odin
phone = %coalesce @mobile @home @work                ; First non-null
status = %ifElse @is_active "A" "I"                  ; true→"A", false→"I"
state = %ifNull @policy.state "TX"                   ; null→"TX"
status_text = %switch @code "A" "Active" "P" "Pending" "Unknown"
```

---

## Type Coercion

| Verb | Syntax | Description |
|------|--------|-------------|
| `coerceString` | `%coerceString @path` | Convert to string |
| `coerceNumber` | `%coerceNumber @path` | Convert to number |
| `coerceInteger` | `%coerceInteger @path` | Convert to integer |
| `coerceBoolean` | `%coerceBoolean @path` | Convert to boolean |
| `coerceDate` | `%coerceDate @path` | Convert to date |
| `tryCoerce` | `%tryCoerce @path` | Auto-detect type |

**Examples:**

```odin
year_str = %coerceString @vehicle.year               ; 2024 → "2024"
amount = %coerceNumber @amount_string                ; "1234.56" → 1234.56
year = %coerceInteger @year_string                   ; "2024" → 2024
is_active = %coerceBoolean @active_flag              ; "Y" → ?true, "N" → ?false
effective = %coerceDate @date_string                 ; "2024-06-15" → date

; tryCoerce auto-detects
value = %tryCoerce @amount                           ; "42" → ##42, "3.14" → #3.14
```

---

## Encoding Transformations

| Verb | Syntax | Description |
|------|--------|-------------|
| `base64Encode` | `%base64Encode @path` | Encode to Base64 |
| `base64Decode` | `%base64Decode @path` | Decode from Base64 |
| `urlEncode` | `%urlEncode @path` | URL percent-encoding |
| `urlDecode` | `%urlDecode @path` | Decode URL encoding |
| `jsonEncode` | `%jsonEncode @path` | Encode as JSON string |
| `jsonDecode` | `%jsonDecode @path` | Parse JSON string |
| `hexEncode` | `%hexEncode @path` | Encode to hexadecimal |
| `hexDecode` | `%hexDecode @path` | Decode from hexadecimal |

---

## Array Operations

| Verb | Syntax | Description |
|------|--------|-------------|
| `at` | `%at @array index` | Get element at index |
| `filter` | `%filter @array "field" "op" value` | Filter by field condition |
| `flatten` | `%flatten @array` | Flatten nested arrays |
| `distinct` | `%distinct @array` | Unique values only |
| `sort` | `%sort @array` | Sort ascending |
| `sortDesc` | `%sortDesc @array` | Sort descending |
| `sortBy` | `%sortBy @array "field"` | Sort by field |
| `reverse` | `%reverse @array` | Reverse order |
| `reduce` | `%reduce @array "verb" initialValue` | Fold array to single value using verb |
| `pivot` | `%pivot @array "keyField" "valueField"` | Array of objects → object keyed by field |
| `unpivot` | `%unpivot @object "keyName" "valueName"` | Object → array of {key, value} objects |

**Examples:**

```odin
second_vehicle = %at @vehicles ##1
active_coverages = %filter @coverages "status" "=" "active"
unique_states = %distinct @addresses.state
by_premium = %sortBy @coverages "premium"
```

---

## Generation

| Verb | Syntax | Description |
|------|--------|-------------|
| `uuid` | `%uuid` | Generate random UUID v4 |
| `uuid` | `%uuid @seed` | Deterministic UUID v5 |
| `sequence` | `%sequence "name"` | Auto-incrementing |

---

## Additional Operations

### Geo/Spatial

| Verb | Syntax | Description |
|------|--------|-------------|
| `distance` | `%distance @lat1 @lon1 @lat2 @lon2 [unit]` | Great-circle distance |
| `inBoundingBox` | `%inBoundingBox @lat @lon @minLat @minLon @maxLat @maxLon` | Point in box |
| `bearing` | `%bearing @lat1 @lon1 @lat2 @lon2` | Initial bearing |

### Text Processing

| Verb | Syntax | Description |
|------|--------|-------------|
| `tokenize` | `%tokenize @text [delimiter]` | Split into tokens |
| `wordCount` | `%wordCount @text` | Count words |
| `levenshtein` | `%levenshtein @str1 @str2` | Edit distance |
| `soundex` | `%soundex @text` | Phonetic encoding |

### Window/Ranking

| Verb | Syntax | Description |
|------|--------|-------------|
| `rowNumber` | `%rowNumber @array` | Assign row numbers |
| `rank` | `%rank @array [field] [direction]` | Rank items |
| `lag` | `%lag @array [periods] [default]` | Previous value |
| `lead` | `%lead @array [periods] [default]` | Next value |

### Sampling

| Verb | Syntax | Description |
|------|--------|-------------|
| `sample` | `%sample @array count [seed]` | Random sample |
| `limit` | `%limit @array count` | First N items |
| `fillMissing` | `%fillMissing @array [value] [strategy]` | Replace nulls |

### Collection

| Verb | Syntax | Description |
|------|--------|-------------|
| `toArray` | `%toArray @value` | Wrap in array if not already |
| `toObject` | `%toObject @pairs` | Convert pairs to object |
| `jsonPath` | `%jsonPath @object "$.path"` | Query with JSONPath |

---

## Custom Verbs

Custom verbs use `%&` prefix with namespaced identifier:

```odin
hash = %&com.acme.sha256 @document.content
phone = %&org.example.normalizePhone @contact.phone
```

**Reserved Namespaces:**

| Namespace | Reserved For |
|-----------|--------------|
| `org.odin.*` | Official ODIN extensions |
| `org.odin.transform.*` | Official transform verbs |
| `org.odin.schema.*` | Official schema types |

Use reverse-domain naming for custom extensions: `%&com.yourcompany.verb`

---

*See also: [Core](Core.md) | [Modifiers](Modifiers.md) | [Formats](Formats.md) | [Reference](Reference.md)*

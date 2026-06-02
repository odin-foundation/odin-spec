# ODIN Transform 1.0: Verbs

<!-- LLM:
PURPOSE: Transformation verb reference with syntax and examples
STRING: concat upper lower capitalize titleCase trim substring replace split join padLeft padRight truncate mask camelCase snakeCase kebabCase pascalCase slugify reverseString repeat normalizeSpace leftOf rightOf wrap center match extract matches stripAccents clean escapeHtml unescapeHtml escapeXml stripTags template
NUMERIC: formatNumber formatInteger formatCurrency abs round floor ceil multiply divide add subtract mod negate sign trunc random minOf maxOf formatPercent isFinite isNaN parseInt safeDivide formatLocaleNumber gcd lcm factorial
EXPR: %expr "formula" [@bindings] - infix arithmetic macro compiled to a verb tree at parse time (+ - * / % ^, unary -, functions abs floor ceil trunc round sqrt pow min max); variables resolve under the bindings object
DATE: formatDate formatTime formatTimestamp parseDate parseTimestamp addDays addMonths addYears dateDiff today now addHours addMinutes addSeconds startOfDay endOfDay startOfMonth endOfMonth startOfYear endOfYear dayOfWeek weekOfYear quarter isLeapYear isBefore isAfter isBetween toUnix fromUnix formatLocaleDate daysBetweenDates ageFromDate isValidDate
LOOKUP: lookup lookupDefault (TABLE.column @match1 @match2) — TABLE.column quoting optional, variadic arity
AGGREGATE: accumulate set sum count min max avg first last countIf sumIf avgIf
CONDITIONAL: coalesce ifElse ifNull ifEmpty switch
LOGIC: and or not xor eq ne lt lte gt gte between isNull isString isNumber isBoolean isArray isObject isDate typeOf cond assert
OBJECT: keys values entries has get merge pick omit fromEntries invert defaults renameKeys compactObject
ARRAY: at filter flatten distinct sort sortBy reverse map indexOf slice every some find findIndex includes concatArrays zip groupBy partition take drop chunk range compact pluck unique cumsum cumprod shift diff pctChange dedupe intersection union difference symmetricDifference countBy keyBy explode window
COERCE: coerceString coerceNumber coerceInteger coerceBoolean coerceDate coerceTimestamp tryCoerce toArray toObject
STATISTICAL: std variance median mode percentile correlation zscore
FINANCIAL: compound discount pmt fv pv npv irr rate nper depreciation xnpv xirr
ENCODING: base64Encode base64Decode urlEncode urlDecode jsonEncode jsonDecode hexEncode hexDecode sha256 sha1 sha512 md5 crc32 base64urlEncode base64urlDecode hmac parseUrl buildUrl parseQuery buildQuery stableStringify canonicalHash
GENERATION: uuid nanoid sequence resetSequence
GEO: distance inBoundingBox bearing midpoint toRadians toDegrees
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
| `pad` | `%pad @path len "char"` | Right-pad to length |
| `reverseString` | `%reverseString @path` | Reverse character order |
| `repeat` | `%repeat @path count` | Repeat string N times |
| `normalizeSpace` | `%normalizeSpace @path` | Collapse and trim whitespace |
| `leftOf` | `%leftOf @path "delim"` | Text before first delimiter |
| `rightOf` | `%rightOf @path "delim"` | Text after first delimiter |
| `wrap` | `%wrap @path width` | Word-wrap at width |
| `center` | `%center @path width ["char"]` | Center-pad to width |
| `match` | `%match @path "regex"` | Boolean: regex matches |
| `matches` | `%matches @path "regex"` | Boolean: regex matches (alias of `match`) |
| `extract` | `%extract @path "regex" [group]` | Extract regex capture group (default 0) |
| `stripAccents` | `%stripAccents @path` | Remove diacritical marks |
| `clean` | `%clean @path` | Strip control chars, normalize whitespace |
| `escapeHtml` | `%escapeHtml @path` | Escape `&<>"'` as HTML entities |
| `unescapeHtml` | `%unescapeHtml @path` | Decode HTML entities (named, numeric, hex) |
| `escapeXml` | `%escapeXml @path` | Escape `&<>"'` as XML entities (apostrophe as `&apos;`) |
| `stripTags` | `%stripTags @path` | Remove HTML/XML tags, keep text |
| `template` | `%template "Hi {name}" @data` | Substitute `{key}` placeholders from an object |

### Case Conversion

| Verb | Syntax | Description |
|------|--------|-------------|
| `camelCase` | `%camelCase @path` | Convert to camelCase |
| `pascalCase` | `%pascalCase @path` | Convert to PascalCase |
| `snakeCase` | `%snakeCase @path` | Convert to snake_case |
| `kebabCase` | `%kebabCase @path` | Convert to kebab-case |
| `slugify` | `%slugify @path` | URL-safe slug (lowercase, hyphenated) |

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
| `sign` | `%sign @path` | Sign of number: -1, 0, or 1 |
| `trunc` | `%trunc @path` | Truncate toward zero |
| `safeDivide` | `%safeDivide @num @denom @default` | Divide, returning default on zero denominator |
| `minOf` | `%minOf @a @b ...` | Minimum of arguments |
| `maxOf` | `%maxOf @a @b ...` | Maximum of arguments |
| `parseInt` | `%parseInt @path [radix]` | Parse string to integer (radix 2-36, default 10) |
| `random` | `%random [min] [max] [seed]` | Random number; seed makes it deterministic |
| `isFinite` | `%isFinite @path` | Boolean: number is finite |
| `isNaN` | `%isNaN @path` | Boolean: number is NaN |
| `formatPercent` | `%formatPercent @path [decimals]` | Format as percentage string (default 2 decimals) |
| `formatLocaleNumber` | `%formatLocaleNumber @path [locale]` | Locale-aware number formatting |
| `gcd` | `%gcd @a @b` | Greatest common divisor of two integers |
| `lcm` | `%lcm @a @b` | Least common multiple of two integers |
| `factorial` | `%factorial @n` | Factorial of n (0 <= n <= 18) |

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
| `npv` | `%npv @rate @cashflows` | Net present value of a cash-flow series |
| `irr` | `%irr @cashflows [guess]` | Internal rate of return |
| `rate` | `%rate @periods @pmt @pv @fv` | Interest rate per period |
| `nper` | `%nper @rate @pmt @pv @fv` | Number of periods |
| `depreciation` | `%depreciation @cost @salvage @life` | Straight-line depreciation per period |
| `xnpv` | `%xnpv @rate @amounts @dates` | Net present value of cash flows on specific dates (365-day basis) |
| `xirr` | `%xirr @amounts @dates [guess]` | Internal rate of return for cash flows on specific dates |

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
| `zscore` | `%zscore @value @array` | Standard score of value vs. dataset (population std) |

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
| `nextBusinessDay` | `%nextBusinessDay @date` | The next Mon-Fri date strictly after the input |
| `formatDuration` | `%formatDuration @duration` | Human-readable duration from an ISO 8601 string or a number of seconds |
| `addHours` | `%addHours @date hours` | Add hours (returns ISO timestamp) |
| `addMinutes` | `%addMinutes @date minutes` | Add minutes (returns ISO timestamp) |
| `addSeconds` | `%addSeconds @date seconds` | Add seconds (returns ISO timestamp) |
| `startOfDay` | `%startOfDay @date` | Start of day (00:00:00.000) |
| `endOfDay` | `%endOfDay @date` | End of day (23:59:59.999) |
| `startOfMonth` | `%startOfMonth @date` | First day of month |
| `endOfMonth` | `%endOfMonth @date` | Last day of month |
| `startOfYear` | `%startOfYear @date` | First day of year (Jan 1) |
| `endOfYear` | `%endOfYear @date` | Last day of year (Dec 31) |
| `dayOfWeek` | `%dayOfWeek @date` | ISO weekday (1=Mon ... 7=Sun) |
| `weekOfYear` | `%weekOfYear @date` | ISO week number (1-53) |
| `quarter` | `%quarter @date` | Calendar quarter (1-4) |
| `isLeapYear` | `%isLeapYear @date` | Boolean: year is a leap year |
| `isBefore` | `%isBefore @date1 @date2` | Boolean: date1 before date2 |
| `isAfter` | `%isAfter @date1 @date2` | Boolean: date1 after date2 |
| `isBetween` | `%isBetween @date @start @end` | Boolean: date within range (inclusive) |
| `toUnix` | `%toUnix @date` | Unix timestamp (seconds since epoch) |
| `fromUnix` | `%fromUnix @timestamp` | Unix timestamp to ISO string |
| `daysBetweenDates` | `%daysBetweenDates @start @end` | Whole days between two dates |
| `ageFromDate` | `%ageFromDate @birthDate [@asOfDate]` | Age in complete years |
| `isValidDate` | `%isValidDate @value "format"` | Boolean: string is a valid date in format |
| `formatLocaleDate` | `%formatLocaleDate @date [locale]` | Locale-aware date formatting |

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

**Note:** Quoting the `TABLE.column` argument is **optional** — both `%lookup "TABLE.column" @match` and `%lookup TABLE.column @match` work. The unquoted form is reassembled into a single `TABLE.column` reference during parsing.

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
| `set` | `%set name value` | Set accumulator to a value (replaces current) |
| `sum` | `%sum @array.field` | Sum field values |
| `count` | `%count @array` | Count items |
| `min` | `%min @array.field` | Minimum |
| `max` | `%max @array.field` | Maximum |
| `avg` | `%avg @array.field` | Average |
| `first` | `%first @array` | First item |
| `last` | `%last @array` | Last item |
| `countIf` | `%countIf @array "field" "op" value` | Count items matching a condition |
| `sumIf` | `%sumIf @array "field" "op" value ["sumField"]` | Sum a field over matching items |
| `avgIf` | `%avgIf @array "field" "op" value ["avgField"]` | Average a field over matching items |

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

## Logic & Comparison

### Boolean Logic

| Verb | Syntax | Description |
|------|--------|-------------|
| `and` | `%and @a @b` | Logical AND |
| `or` | `%or @a @b` | Logical OR |
| `not` | `%not @value` | Logical negation |
| `xor` | `%xor @a @b` | Exclusive OR (exactly one truthy) |

### Comparison

| Verb | Syntax | Description |
|------|--------|-------------|
| `eq` | `%eq @a @b` | Boolean: equal |
| `ne` | `%ne @a @b` | Boolean: not equal |
| `lt` | `%lt @a @b` | Boolean: less than |
| `lte` | `%lte @a @b` | Boolean: less than or equal |
| `gt` | `%gt @a @b` | Boolean: greater than |
| `gte` | `%gte @a @b` | Boolean: greater than or equal |
| `between` | `%between @value @min @max` | Boolean: numeric range (inclusive) |

### Type Checks

| Verb | Syntax | Description |
|------|--------|-------------|
| `isNull` | `%isNull @value` | Boolean: value is null |
| `isString` | `%isString @value` | Boolean: value is a string |
| `isNumber` | `%isNumber @value` | Boolean: value is integer/number/currency |
| `isBoolean` | `%isBoolean @value` | Boolean: value is a boolean |
| `isArray` | `%isArray @value` | Boolean: value is an array |
| `isObject` | `%isObject @value` | Boolean: value is an object |
| `isDate` | `%isDate @value` | Boolean: value is a date/timestamp |
| `typeOf` | `%typeOf @value` | Type name as a string |

### Conditional & Validation

| Verb | Syntax | Description |
|------|--------|-------------|
| `cond` | `%cond @c1 @v1 [@c2 @v2 ...] @default` | First value whose condition is truthy |
| `assert` | `%assert @condition [message]` | Returns condition if truthy, else null |

**Examples:**

```odin
in_range = %and %gte @age ##18 %lte @age ##65
is_valid = %gt @amount ##0
grade = %cond %gte @score ##90 "A" %gte @score ##80 "B" "F"
```

---

## Object Operations

| Verb | Syntax | Description |
|------|--------|-------------|
| `keys` | `%keys @object` | Array of object keys |
| `values` | `%values @object` | Array of object values |
| `entries` | `%entries @object` | Array of `[key, value]` pairs |
| `has` | `%has @object "key"` | Boolean: object has key (dot paths supported) |
| `get` | `%get @object "path" [default]` | Safe path access with optional default |
| `merge` | `%merge @obj1 @obj2` | Shallow merge (obj2 overrides obj1) |
| `pick` | `%pick @object "k1" "k2"` | Keep only the named keys |
| `omit` | `%omit @object "k1" "k2"` | Drop the named keys |
| `fromEntries` | `%fromEntries @pairs` | Build an object from `[key, value]` pairs |
| `invert` | `%invert @object` | Swap keys and values |
| `defaults` | `%defaults @object @fallback` | Fill keys the base object lacks |
| `renameKeys` | `%renameKeys @object @mapping` | Rename keys via an old-to-new mapping |
| `compactObject` | `%compactObject @object` | Drop null, empty-string, empty-array, empty-object values |

---

## Type Coercion

| Verb | Syntax | Description |
|------|--------|-------------|
| `coerceString` | `%coerceString @path` | Convert to string |
| `coerceNumber` | `%coerceNumber @path` | Convert to number |
| `coerceInteger` | `%coerceInteger @path` | Convert to integer |
| `coerceBoolean` | `%coerceBoolean @path` | Convert to boolean |
| `coerceDate` | `%coerceDate @path` | Convert to date |
| `coerceTimestamp` | `%coerceTimestamp @path` | Convert to timestamp |
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
| `base64urlEncode` | `%base64urlEncode @path` | URL-safe Base64, no padding |
| `base64urlDecode` | `%base64urlDecode @path` | Decode URL-safe Base64 |

### URLs & Query Strings

| Verb | Syntax | Description |
|------|--------|-------------|
| `parseUrl` | `%parseUrl @url` | Split a URL into `{scheme, host, port, path, query, fragment}` (query keys sorted) |
| `buildUrl` | `%buildUrl @parts` | Inverse of `parseUrl` (query keys sorted) |
| `parseQuery` | `%parseQuery @qs` | Parse a query string into an object (keys sorted) |
| `buildQuery` | `%buildQuery @object` | Serialize an object to a query string (keys sorted) |

### Hashing & Checksums

| Verb | Syntax | Description |
|------|--------|-------------|
| `sha256` | `%sha256 @path` | SHA-256 hash (lowercase hex) |
| `sha1` | `%sha1 @path` | SHA-1 hash (lowercase hex) |
| `sha512` | `%sha512 @path` | SHA-512 hash (lowercase hex) |
| `md5` | `%md5 @path` | MD5 hash (lowercase hex; not for security) |
| `crc32` | `%crc32 @path` | CRC-32 checksum (8-char hex) |
| `hmac` | `%hmac @message "key" ["alg"]` | Keyed hash, hex output (default sha256) |

### Canonical Form

| Verb | Syntax | Description |
|------|--------|-------------|
| `stableStringify` | `%stableStringify @value` | Canonical JSON with object keys sorted recursively |
| `canonicalHash` | `%canonicalHash @value` | SHA-256 of the canonical form (order-independent fingerprint) |

---

## Array Operations

| Verb | Syntax | Description |
|------|--------|-------------|
| `at` | `%at @array index` | Get element at index (negative indices supported) |
| `slice` | `%slice @array start [end]` | Sub-array between indices (negative supported) |
| `indexOf` | `%indexOf @array value` | Index of value (-1 if absent) |
| `map` | `%map @array "field"` | Extract one field from every item |
| `pluck` | `%pluck @array "field"` | Extract field values from objects |
| `filter` | `%filter @array "field" "op" value` | Filter by field condition |
| `flatten` | `%flatten @array` | Flatten nested arrays one level |
| `distinct` | `%distinct @array ["field"]` | Unique values (optionally by field) |
| `unique` | `%unique @array` | Unique values (alias of `distinct`) |
| `dedupe` | `%dedupe @array "keyField"` | Remove duplicate objects by key field |
| `compact` | `%compact @array` | Drop null, undefined, and empty-string items |
| `sort` | `%sort @array ["field"] ["desc"]` | Sort ascending (optional field/direction) |
| `sortDesc` | `%sortDesc @array` | Sort descending |
| `sortBy` | `%sortBy @array "field"` | Sort by field |
| `reverse` | `%reverse @array` | Reverse order |
| `take` | `%take @array count` | First N elements |
| `drop` | `%drop @array count` | Skip first N elements |
| `chunk` | `%chunk @array size` | Split into fixed-size chunks |
| `range` | `%range start end [step]` | Generate numeric array (end exclusive) |
| `includes` | `%includes @array value` | Boolean: array contains value |
| `every` | `%every @array "field" "op" value` | Boolean: all items match condition |
| `some` | `%some @array "field" "op" value` | Boolean: any item matches condition |
| `find` | `%find @array "field" "op" value` | First item matching condition |
| `findIndex` | `%findIndex @array "field" "op" value` | Index of first matching item (-1 if none) |
| `partition` | `%partition @array "field" "op" value` | Split into [matching, non-matching] |
| `groupBy` | `%groupBy @array "field"` | Group into `{key, items}` objects |
| `concatArrays` | `%concatArrays @arr1 @arr2` | Concatenate two arrays |
| `zip` | `%zip @arr1 @arr2` | Pair elements into `[a, b]` tuples |
| `reduce` | `%reduce @array "verb" initialValue` | Fold array to single value using verb |
| `pivot` | `%pivot @array "keyField" "valueField"` | Array of objects → object keyed by field |
| `unpivot` | `%unpivot @object "keyName" "valueName"` | Object → array of {key, value} objects |
| `intersection` | `%intersection @a @b` | Distinct elements in both arrays |
| `union` | `%union @a @b` | Distinct elements from both arrays |
| `difference` | `%difference @a @b` | Distinct elements of the first not in the second |
| `symmetricDifference` | `%symmetricDifference @a @b` | Distinct elements in exactly one array |
| `countBy` | `%countBy @array ["field"]` | Count items per field value (keys sorted) |
| `keyBy` | `%keyBy @array "field"` | Index items by a field value (last wins) |
| `explode` | `%explode @array "field"` | One row per element of an array-valued field |
| `window` | `%window @array n` | Overlapping consecutive slices of length n |

**Filter operators** (shared by `filter`, `every`, `some`, `find`, `findIndex`, `partition`, `countIf`, `sumIf`, `avgIf`): `=`, `!=`, `<`, `<=`, `>`, `>=`, `contains`, `startsWith`, `endsWith`.

### Time-Series Array Verbs

| Verb | Syntax | Description |
|------|--------|-------------|
| `cumsum` | `%cumsum @array` | Running cumulative sum |
| `cumprod` | `%cumprod @array` | Running cumulative product |
| `shift` | `%shift @array [periods] [fillValue]` | Shift elements, filling gaps |
| `diff` | `%diff @array [periods]` | Difference between elements N apart |
| `pctChange` | `%pctChange @array [periods]` | Percentage change between elements N apart |

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
| `nanoid` | `%nanoid [size] [@seed]` | URL-safe ID (default 21 chars; seed = deterministic) |
| `sequence` | `%sequence "name" [start]` | Auto-incrementing named counter |
| `resetSequence` | `%resetSequence "name" [value]` | Reset a named sequence |

---

## Additional Operations

### Geo/Spatial

| Verb | Syntax | Description |
|------|--------|-------------|
| `distance` | `%distance @lat1 @lon1 @lat2 @lon2 [unit]` | Great-circle distance |
| `inBoundingBox` | `%inBoundingBox @lat @lon @minLat @minLon @maxLat @maxLon` | Point in box |
| `bearing` | `%bearing @lat1 @lon1 @lat2 @lon2` | Initial bearing |
| `midpoint` | `%midpoint @lat1 @lon1 @lat2 @lon2` | Great-circle midpoint `{lat, lon}` |
| `toRadians` | `%toRadians @degrees` | Degrees to radians |
| `toDegrees` | `%toDegrees @radians` | Radians to degrees |

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

## Expression Macro (`%expr`)

`%expr` evaluates an infix arithmetic formula. It is a **parse-time macro**, not a
runtime evaluator: the formula string is compiled once into a tree of ordinary
transform verbs (`%add`, `%multiply`, `%pow`, ...) when the transform is parsed.
The arithmetic is therefore performed entirely by the existing deterministic verbs,
so `%expr` introduces no new numeric behavior and is byte-identical across SDKs.

```odin
; literal formula (no variables, no bindings)
answer = %expr "2^3^2"                       ; -> 512

; variables resolve under an explicit bindings object
{v}
a = #2
b = #3
c = #4
x = #5

quadratic = %expr "a*x^2 + b*x + c" @.v      ; a -> @.v.a, x -> @.v.x ... -> 69
```

**Bindings are explicit.** A bare identifier in the formula reads the matching field
of the bindings object passed as the second argument (`a` reads `@.v.a`). A formula
that uses a variable without a bindings object is a compile error (`T015`). This keeps
data dependencies visible rather than resolving names implicitly.

**Operators** compile to verbs:

| Operator | Verb | Notes |
|----------|------|-------|
| `+` `-` `*` `/` `%` | `add` `subtract` `multiply` `divide` `mod` | `/` by zero yields `~`, following `%divide` |
| unary `-` `+` | `negate` / identity | |
| `^` | `pow` | right-associative |

**Precedence**, high to low:

1. parentheses and function calls
2. `^` power (right-associative): `2^3^2` = `2^(3^2)` = `512`
3. unary `-` / `+` (binds looser than `^`): `-2^2` = `-(2^2)` = `-4`; use `(-2)^2` = `4`
4. `*` `/` `%` (left-associative)
5. `+` `-` (left-associative)

**Functions** (whitelist of deterministic numeric verbs only):
`abs` `floor` `ceil` `trunc` `round` `sqrt` `pow` `min` `max`. `round(x)` defaults to
0 decimals; `min`/`max` take two or more arguments. Calling any other function (for
example `sin`, `exp`, `ln`) is a compile error, because those are not byte-identical
across language runtimes.

**Errors** (all `T015`, raised at parse time): unknown function, wrong number of
function arguments, malformed syntax (unexpected or missing token), unbalanced
parentheses, and a variable used without a bindings object.

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

# ODIN LLM Reference v1.1

Ultra-dense reference for LLM context. Machine-readable, token-optimized.

---

## ODIN DOCUMENT SYNTAX

### Basic Assignment
```odin
field = "string value"        ; quoted string
field = #3.14159              ; number (decimal)
field = ##42                  ; integer (whole number)
field = #$99.99               ; currency (2 decimal)
field = ?true                 ; boolean (?true or ?false)
field = 2024-06-15            ; date (ISO 8601, no quotes)
field = 2024-06-15T14:30:00Z  ; timestamp (ISO 8601)
field = ~                     ; null
field = @other.path           ; reference to another field
field = ^SGVsbG8=             ; binary (base64)
```

### Triple-Quoted Multiline Strings
```odin
note = """line one
line two
line three"""                ; verbatim across newlines, no escape processing
; Unterminated """ = parse error P004
```

### Modifiers (inline metadata)
```odin
name = !"John"                ; ! = required
ssn = *"123-45-6789"          ; * = confidential
old_field = -"deprecated"     ; - = deprecated
email = !*"a@b.com"           ; can combine: required + confidential
```

### Nested Paths
```odin
person.name.first = "John"
person.name.last = "Smith"
person.age = ##30
address.city = "Austin"
address.state = "TX"
```

### Arrays (0-based, contiguous indices required)
```odin
items[0].name = "First"
items[0].price = #10.00
items[1].name = "Second"
items[1].price = #20.00
; items[5] without 2-4 = INVALID (must be contiguous)
```

### Headers (grouping context)
```odin
{person}                      ; context header - paths below are relative
name = "John"                 ; = person.name
age = ##30                    ; = person.age

{person.address}              ; nested context
city = "Austin"               ; = person.address.city
state = "TX"                  ; = person.address.state
```

### Tabular Arrays (compact for flat objects)
```odin
{items[] : sku, name, qty, price}
"ABC-001", "Widget", ##10, #$5.99
"ABC-002", "Gadget", ##5, #$12.50
"XYZ-100", "Cable", ##20, #$3.25
; Expands to items[0].sku="ABC-001", items[0].name="Widget", etc.
```

### Primitive Arrays (values only)
```odin
{tags[] : ~}                  ; ~ marker = primitive array mode
"urgent"
"important"
"reviewed"
; Expands to tags[0]="urgent", tags[1]="important", etc.
```

### Comments
```odin
; This is a comment (semicolon to end of line)
name = "John"  ; inline comment
```

### Document Chaining
```odin
---                           ; document separator
; Second document in same file
```

### Chaining Semantics (current-state computation)
```odin
; Later documents overlay earlier ones; same path replaces value.
field = ~                     ; null in later doc REMOVES the field from result
field[] = ~                   ; clears (empties) an inherited array
; Arrays merge by index; each doc's {$} header is independent.
```

### Empty/Null Arrays
```odin
items[] = ~                   ; explicit empty array
```

### Canonical Form Rules
1. Sorted by path alphabetically
2. No headers (explicit full paths)
3. No comments
4. No blank lines
5. LF line endings
6. Bare strings where valid (no unnecessary quotes)

### Valid ODIN Document Example
```odin
{$}
odin = "1.0.0"

{policy}
id = "POL-12345"
effective = 2024-06-15
premium = #$1234.56
active = ?true

{policy.insured}
name = !"John Smith"
ssn = *"123-45-6789"
dob = 1985-03-15

{policy.vehicles[] : vin, year, make, model}
"1HGCM82633A004352", ##2020, "Honda", "Accord"
"2T1BR32E85C456789", ##2022, "Toyota", "Camry"

{policy.coverages[] : code, limit, premium}
"BI", ##100000, #$450.00
"PD", ##50000, #$200.00
"COMP", ##25000, #$150.00
```

---

## NOTATION SYMBOLS

|Sym|Type|Example|
|---|---|---|
|`#`|number|`#3.14`|
|`##`|integer|`##42`|
|`#$`|currency|`#$99.99`|
|`?`|boolean|`?true` `?false`|
|`@`|reference|`@path.to.field`|
|`^`|binary|`^base64data`|
|`~`|null|`~`|
|`!`|required mod|`!"value"`|
|`*`|confidential mod|`*"ssn"`|
|`-`|deprecated mod|`-"old"`|

## PATHS

```
field = "value"           ; simple
obj.field = "value"       ; nested
arr[0].field = "value"    ; array index (0-based, contiguous)
{header}                  ; context header
{arr[] : col1, col2}      ; tabular array
{arr[] : ~}               ; primitive array
```

## TRANSFORM STRUCTURE

```odin
{$}
odin = "1.0.0"
transform = "1.0.0"
direction = "source->target"  ; json->json, odin->xml, fixed-width->odin, etc.
enforceConfidential = "redact" | "mask"  ; optional

{$const}
KEY = "value"

{$accumulator}
counter = ##0

{$table.NAME[col1, col2, col3]}
val1, val2, val3

{SegmentName}
field = "@source.path"
field = "%verb @arg1 @arg2"

{ArraySegment[]}
_loop = "@"
_from = "sourceArray"
field = "@.subfield"

{TestResult}  ; for self-tests
passed = "@$accumulator.passed"
failed = "@$accumulator.failed"
```

## VERB ARITY MAP

-1 = variadic. Format: `verb:arity`

### CORE (12)
```
concat:-1 upper:1 lower:1 trim:1 trimLeft:1 trimRight:1 coalesce:-1 ifNull:2 ifEmpty:2 ifElse:3 lookup:-1 lookupDefault:-1
```

### COERCION (9)
```
coerceString:1 coerceNumber:1 coerceInteger:1 coerceBoolean:1 coerceDate:1 coerceTimestamp:1 tryCoerce:1 toArray:1 toObject:1
```

### STRING (38)
```
capitalize:1 titleCase:1 length:1 contains:2 startsWith:2 endsWith:2 substring:3 replace:3 replaceRegex:3 padLeft:3 padRight:3 pad:3 truncate:3 split:3 join:2 mask:2 reverseString:1 repeat:2 camelCase:1 snakeCase:1 kebabCase:1 pascalCase:1 slugify:1 match:2 extract:3 normalizeSpace:1 leftOf:2 rightOf:2 wrap:2 center:3 matches:2 stripAccents:1 clean:1 tokenize:2 wordCount:1 levenshtein:2 soundex:1 formatPhone:2
```

### NUMERIC (26)
```
formatNumber:2 formatInteger:1 formatCurrency:1 abs:1 round:2 floor:1 ceil:1 add:2 subtract:2 multiply:2 divide:2 mod:2 negate:1 switch:-1 sign:1 trunc:1 random:3 minOf:-1 maxOf:-1 formatPercent:2 isFinite:1 isNaN:1 parseInt:2 formatLocaleNumber:2 safeDivide:3 convertUnit:3
```

### DATETIME (36)
```
formatDate:2 parseDate:2 today:0 now:0 formatTime:2 formatTimestamp:2 parseTimestamp:2 addDays:2 addMonths:2 addYears:2 addHours:2 addMinutes:2 addSeconds:2 dateDiff:3 startOfDay:1 endOfDay:1 startOfMonth:1 endOfMonth:1 startOfYear:1 endOfYear:1 dayOfWeek:1 weekOfYear:1 quarter:1 isLeapYear:1 isBefore:2 isAfter:2 isBetween:3 toUnix:1 fromUnix:1 formatLocaleDate:2 daysBetweenDates:2 ageFromDate:2 isValidDate:2 businessDays:2 nextBusinessDay:1 formatDuration:1
```

### AGGREGATION (9)
```
accumulate:2 set:2 sum:1 count:1 min:1 max:1 avg:1 first:1 last:1
```

### GENERATION (4)
```
uuid:1 sequence:1 resetSequence:1 nanoid:2
```

### ENCODING (14)
```
base64Encode:1 base64Decode:1 urlEncode:1 urlDecode:1 jsonEncode:1 jsonDecode:1 hexEncode:1 hexDecode:1 sha256:1 md5:1 sha1:1 sha512:1 crc32:1 jsonPath:2
```

### ARRAY (43)
```
filter:4 flatten:1 distinct:1 sort:1 sortDesc:1 sortBy:2 map:2 indexOf:2 at:2 slice:3 reverse:1 every:4 some:4 find:4 findIndex:4 includes:2 concatArrays:2 zip:2 groupBy:2 partition:4 take:2 drop:2 chunk:2 range:3 compact:1 pluck:2 unique:1 cumsum:1 cumprod:1 shift:3 diff:2 pctChange:2 dedupe:2 rowNumber:1 rank:3 lag:3 lead:3 sample:3 limit:2 fillMissing:3 reduce:3 pivot:3 unpivot:3
```

### FINANCIAL (31)
```
log:2 ln:1 log10:1 exp:1 pow:2 sqrt:1 compound:3 discount:3 pmt:3 fv:3 pv:3 npv:2 irr:2 rate:4 nper:4 depreciation:3 std:1 stdSample:1 variance:1 varianceSample:1 median:1 mode:1 percentile:2 quantile:2 covariance:2 correlation:2 clamp:3 interpolate:5 weightedAvg:2 zscore:2 movingAvg:2
```

### LOGIC (21)
```
and:2 or:2 not:1 xor:2 eq:2 ne:2 lt:2 lte:2 gt:2 gte:2 between:3 isNull:1 isString:1 isNumber:1 isBoolean:1 isArray:1 isObject:1 isDate:1 typeOf:1 cond:-1 assert:2
```

### OBJECT (6)
```
keys:1 values:1 entries:1 has:2 get:3 merge:2
```

### GEO (6)
```
distance:5 inBoundingBox:6 toRadians:1 toDegrees:1 bearing:4 midpoint:4
```

## VERB SYNTAX QUICK REF

```
%concat @a "sep" @b ...        ; join values
%upper @path                   ; UPPERCASE
%lower @path                   ; lowercase
%trim @path                    ; strip whitespace
%substring @path start len     ; extract (0-based)
%replace @path "find" "repl"   ; replace all
%padLeft @path len "char"      ; left pad
%padRight @path len "char"     ; right pad
%split @path "delim" index     ; split and get
%join @array "delim"           ; join array

%formatNumber @path decimals   ; "3.14"
%formatInteger @path           ; "42"
%formatCurrency @path          ; "99.99"
%round @path decimals          ; round
%floor @path                   ; floor
%ceil @path                    ; ceil
%add @a @b                     ; a + b
%subtract @a @b                ; a - b
%multiply @a @b                ; a * b
%divide @a @b                  ; a / b
%safeDivide @a @b @default     ; a / b or default if b=0
%mod @a @b                     ; a % b
%abs @path                     ; |x|
%negate @path                  ; -x
%sign @path                    ; -1, 0, 1
%trunc @path                   ; truncate toward 0
%minOf @a @b ...               ; min of values
%maxOf @a @b ...               ; max of values

%formatDate @path "pattern"    ; date->string
%parseDate @str "pattern"      ; string->date
%today                         ; current date
%now                           ; current timestamp
%addDays @date days            ; add days
%addMonths @date months        ; add months
%addYears @date years          ; add years
%dateDiff @d1 @d2 "unit"       ; diff in days/months/years
%daysBetweenDates @d1 @d2      ; days between
%ageFromDate @birth @asOf      ; age in years

%lookup TABLE.col @match ...   ; table lookup
%lookupDefault TABLE.col @match "default"
%switch @val "v1" "r1" "v2" "r2" "default"

%coalesce @a @b @c ...         ; first non-null
%ifNull @path @fallback        ; fallback if null
%ifEmpty @path @fallback       ; fallback if empty
%ifElse @cond @then @else      ; conditional

%eq @a @b                      ; a == b
%ne @a @b                      ; a != b
%lt @a @b                      ; a < b
%lte @a @b                     ; a <= b
%gt @a @b                      ; a > b
%gte @a @b                     ; a >= b
%between @val @min @max        ; min <= val <= max
%and @a @b                     ; a && b
%or @a @b                      ; a || b
%not @a                        ; !a
%isNull @path                  ; is null?
%isString @path                ; is string?
%isNumber @path                ; is number?
%isBoolean @path               ; is boolean?
%isArray @path                 ; is array?
%isObject @path                ; is object?

%accumulate name value         ; add to accumulator
%set name value                ; set accumulator
%sum @array.field              ; sum field
%count @array                  ; count items
%min @array.field              ; min value
%max @array.field              ; max value
%avg @array.field              ; average
%first @array                  ; first item
%last @array                   ; last item

%filter @arr "field" "op" val  ; filter array
%sort @array                   ; sort asc
%sortDesc @array               ; sort desc
%sortBy @array "field"         ; sort by field
%at @array index               ; get by index
%slice @array start end        ; slice
%flatten @array                ; flatten nested
%distinct @array               ; unique values
%reverse @array                ; reverse order
%pluck @array "field"          ; extract field
%dedupe @array "keyField"      ; remove duplicates

%uuid                          ; random UUID
%uuid @seed                    ; deterministic UUID
%sequence "name"               ; auto-increment
%nanoid [size] [seed]          ; nano ID

%base64Encode @path            ; encode base64
%base64Decode @path            ; decode base64
%urlEncode @path               ; URL encode
%urlDecode @path               ; URL decode
%jsonEncode @path              ; to JSON string
%jsonDecode @path              ; parse JSON
%sha256 @path                  ; SHA-256 hash

%pow @base @exp                ; base^exp
%sqrt @value                   ; square root
%log @value [base]             ; logarithm
%ln @value                     ; natural log
%exp @value                    ; e^x
%compound @p @r @n             ; P*(1+r)^n
%pmt @p @r @n                  ; loan payment
%std @array                    ; std deviation
%median @array                 ; median
%percentile @array pct         ; percentile
%clamp @val @min @max          ; constrain
%interpolate @x @x1 @y1 @x2 @y2
```

## DATE PATTERNS

```
YYYY = 4-digit year    MM = 2-digit month    DD = 2-digit day
HH = 24-hour           hh = 12-hour          mm = minutes
ss = seconds           SSS = milliseconds    A = AM/PM
Z = timezone
```

Examples: `YYYY-MM-DD` `MMDDYYYY` `MM/DD/YYYY` `YYYY-MM-DDTHH:mm:ssZ`

## MODIFIERS

### POSITIONING (fixed-width)
```
:pos N          ; start position (0-based)
:len N          ; field length
:leftPad "c"    ; pad left with char
:rightPad "c"   ; pad right with char
:truncate       ; truncate to length
```

### JSON
```
:type string|number|boolean   ; force type
:omitNull                     ; omit if null
:omitEmpty                    ; omit if empty
:array                        ; force single-element array
:raw                          ; emit raw JSON
:object {key=@path}           ; inline object construction
```

### VALIDATION
```
:validate "regex"             ; value must match regex (else T013)
:enum v1,v2,v3                ; value must be one of (unquoted list)
:range min..max               ; numeric value within bounds
```

### CONDITIONAL
```
:if path                      ; include if truthy
:if path = value              ; include if equals
:unless path                  ; include if falsy
:default value                ; fallback value
:required                     ; error if missing
```

### SECURITY
```
:confidential                 ; mark for redaction/masking
```

## SEGMENT DIRECTIVES

```
_pass = ##N                   ; execute in pass N (multi-pass)
_loop = "@"                   ; loop context
_from = "path"                ; source array path
:loop path                    ; iterate array
:loop path :as alias          ; iterate with alias (nested loops = repeat :loop path :as alias)
:counter name                 ; loop counter
:if <verb-expr>               ; conditional segment (verb-expression, never quoted infix)
:elif <verb-expr>             ; else-if branch (dangling = error T012)
:else                         ; else branch (dangling = error T012)
:literal                      ; literal block mode (verbatim body, ${...} interpolation)
```

### Inline Discriminator
```odin
{segment :type "value"}       ; matches discriminator value in header
{items[] :type "ITEM"}        ; array segment with discriminator
```

## LOOPS

### Basic Array Loop
```odin
{Vehicles[]}
:loop vehicles
vin = "@.vin"
year = "@.year"
make = "%upper @.make"
```

### Loop with Alias
```odin
{Coverages[]}
:loop vehicles :as veh
:loop .coverages :as cov
vehicle_vin = "@veh.vin"
coverage_code = "@cov.code"
premium = "@cov.premium"
```

### Loop with Counter
```odin
{Items[]}
:loop items
:counter itemNum
sequence = "@itemNum"         ; 1, 2, 3...
name = "@.name"
```

### Loop Context (@.)
```odin
; Inside loop, @. refers to current item
{Orders[]}
:loop orders
id = "@.id"                   ; current order's id
total = "%sum @.items.price"  ; sum of current order's items
customer = "@.customer.name"  ; nested path from current item
```

### Conditional Segments
```odin
; A condition is a verb-expression, NEVER quoted infix.
{PremiumVehicles[]}
:loop vehicles
:if %gt @.premium ##1000
vin = "@.vin"
premium = "@.premium"
```

### Conditional Chains (:if / :elif / :else)
```odin
; First branch whose condition holds emits; rest skipped.
; A dangling :elif/:else (no preceding :if) = error T012.
{HighRisk :if %eq @driver.tier "dui"}
band = "high-risk"

{YoungDriver :elif %lt @driver.age ##25}
band = "young-driver"

{Standard :else}
band = "standard"
```

## IMPORTS

```odin
@import ./tables/territories.odin           ; import file
@import ./mappings/base.odin as base        ; import with alias

; Use imported tables
territory = "%lookup TERRITORY.territory @.state @.zip"

; Use aliased imports
code = "%lookup base.CODES.code @.type"
```

## LOOKUP TABLES

```odin
; Define lookup table with named columns
{$table.STATUS[name, code, description]}
active, "A", "Currently active"
pending, "P", "Awaiting approval"
cancelled, "C", "No longer valid"

; Lookup returning specific column, matching on other columns
status_code = "%lookup STATUS.code @.statusName"
; "active" -> "A"

status_name = "%lookup STATUS.name @.statusCode"
; "A" -> "active" (reverse lookup - same syntax)

; Multi-column match
{$table.RATE[vehicle_type, coverage, base, factor]}
sedan, liability, ##250, #1.15
sedan, collision, ##175, #1.10
truck, liability, ##300, #1.20

base_rate = "%lookup RATE.base @.vehicleType @.coverage"
; "sedan", "liability" -> ##250

; With default fallback
code = "%lookupDefault STATUS.code @.statusName \"XX\""
; unknown -> "XX"
```

## INTERPOLATION (Literal Blocks)

```odin
{segment.HDR}
:literal
"""
HDR|${@policy.number}|${%formatDate @effective "YYYYMMDD"}
"""

; Interpolation syntax:
; ${@path}              - source path value
; ${@.field}            - current loop item field
; ${%verb @arg}         - transformation result
; ${@$accumulator.name} - accumulator value

; Escape literal ${
template = """Price: \${amount} (not interpolated)"""
```

## ERROR HANDLING

```odin
{$target}
format = json
onError = fail           ; fail | warn | skip
onMissing = skip         ; fail | warn | skip | default
onValidation = fail      ; fail | warn | skip
```

### Transform Error Codes (T001-T014)
```
T001 unknown verb         T002 invalid verb args      T003 lookup table not found
T004 lookup key not found T005 source path not found  T006 invalid output format
T007 invalid modifier     T008 accumulator overflow   T009 loop source not array
T010 position overflow    T011 incompatible conversion T012 dangling :elif/:else
T013 validation failed    T014 nested ${} in literal
```

## ODIN SCHEMA SYNTAX

Schema files validate ODIN documents. Schema is written in ODIN notation.

### Type Declarations
```odin
{Person}
name = !""                    ; required string
age = !##                     ; required integer
email = ""                    ; optional string
salary = #$                   ; optional currency
active = ?                    ; optional boolean
created = date                ; date type
updated = timestamp           ; timestamp type
data = ^                      ; binary type
ref = @                       ; reference type
notes = ~""                   ; nullable string
```

### Constraints
```odin
{Policy}
number = !"" :/^POL-\d+$/     ; regex pattern
state = !"" (TX,CA,NY,FL)     ; enum values
year = !## :(1900..2100)      ; range bounds
rate = !# :(0..1)             ; decimal range
premium = !#$ :(0..)          ; min only (no max)
count = !## :(..100)          ; max only (no min)
```

### Array Schemas
```odin
{Order}
id = !""

{Order.items[]}               ; array schema
sku = !""
qty = !## :(1..)
price = !#$
```

### Conditional Fields
```odin
{Vehicle}
type = !"" (car,truck,motorcycle)
bed_length = ## :if type=truck        ; only if truck
engine_cc = ## :if type=motorcycle    ; only if motorcycle
```

### Modifiers in Schema
```odin
{Customer}
name = !""                    ; required
ssn = !*""                    ; required + confidential
old_id = -""                  ; deprecated
```

### Schema Example
```odin
{$}
odin = "1.0.0"
schema = "1.0.0"

{InsurancePolicy}
id = !"" :/^POL-[A-Z0-9]+$/
effective = !date
expiration = !date
status = !"" (active,pending,cancelled,expired)
premium = !#$ :(0..)

{InsurancePolicy.insured}
name = !""
ssn = !*"" :/^\d{3}-\d{2}-\d{4}$/
dob = !date
email = "" :/^[\w.+-]+@[\w-]+\.[a-z]{2,}$/

{InsurancePolicy.vehicles[]}
vin = !"" :/^[A-HJ-NPR-Z0-9]{17}$/
year = !## :(1900..2100)
make = !""
model = !""

{InsurancePolicy.coverages[]}
code = !"" (BI,PD,COMP,COLL,UM,UIM)
limit = !##
deductible = ##
premium = !#$
```

### Schema Symbol Summary
```
! = required field
* = confidential (redact in logs)
- = deprecated
~ = nullable prefix
? = boolean type
# = number type
## = integer type
#$ = currency type
@ = reference type
^ = binary type
date = date type
timestamp = timestamp type

:(min..max)     ; range constraint
:/regex/        ; pattern constraint
(a,b,c)         ; enum constraint
:if field=val   ; conditional field
```

## CDM OUTPUT FORMAT

Transform output uses typed wrappers:
```json
{"field": {"type": "string", "value": "text"}}
{"field": {"type": "integer", "value": 42}}
{"field": {"type": "number", "value": 3.14}}
{"field": {"type": "boolean", "value": true}}
```

## TOTAL VERBS: 255

By category: core(12) coercion(9) string(38) numeric(26) datetime(36) aggregation(9) generation(4) encoding(14) array(43) financial(31) logic(21) object(6) geo(6)

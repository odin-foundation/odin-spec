# %formatPhone

format a phone number by country

**Signature:** `%formatPhone <string> <country:string> -> string`

Strip non-digits and format a phone number for a given country code.

**Transform**

```odin
{out}
out = %formatPhone @.phone "US"
eleven = %formatPhone @.phone11 "US"
wrongCount = %formatPhone @.short "US"
dirty = %formatPhone @.dirty "US"
```

**In**

```odin
phone = "2125551234"
phone11 = "12125551234"
short = "12345"
dirty = "(212) 555-1234"
```

**Out**

```odin
{out}
out = "(212) 555-1234"
eleven = "(212) 555-1234"
wrongCount = "12345"
dirty = "(212) 555-1234"
```

**Notes**

- Two required arguments: the raw number and a country code (US, CA, GB, DE, FR, AU, JP).
- Non-digit characters are stripped before formatting. A wrong digit count for the country returns the original value unchanged.

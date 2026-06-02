# %formatLocaleNumber

locale-grouped number string

**Signature:** `%formatLocaleNumber <number> [string:locale] -> string`

Format a number using locale-specific grouping and decimal separators.

**Transform**

```odin
{out}
v = %formatLocaleNumber @.a "en-US"
millions = %formatLocaleNumber @.b "en-US"
german = %formatLocaleNumber @.a "de-DE"
```

**In**

```odin
a = #1234.56
b = ##1000000
```

**Out**

```odin
{out}
v = "1,234.56"
millions = "1,000,000"
german = "1.234,56"
```

**Notes**

- Pass an explicit locale (e.g. "en-US", "de-DE") for deterministic grouping; "en-US" groups thousands with commas.
- Omitting the locale uses the host system default, so always supply one for reproducible output.
- The result is a string.

**Avoid**

- `v = %formatLocaleNumber @.a`: without an explicit locale the host default locale is used, making the grouping/separator output environment-dependent

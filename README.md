# JSON to Excel Benchmark (Files That Break Converters)

> A small set of JSON test files for checking whether a JSON to Excel or CSV converter preserves your data exactly: 64-bit IDs, leading-zero IDs, formula injection, dates, nested objects, NDJSON, and GST returns.

Most converters work fine on tidy sample data, then silently corrupt the real thing. A 19-digit ID comes back with its last digits replaced by zeros, an account number loses its leading zero, a date reformats. The file still opens and still looks correct, which is exactly what makes it dangerous. These fixtures pack those cases into small files so you can test any tool in seconds.

## The core test: silent corruption

[fixtures/silent-corruption.json](fixtures/silent-corruption.json) packs the tricky cases into one tiny file. Convert it, open the result, and check each cell against the source:

| Test | Pass means |
|------|-----------|
| Large numbers / 64-bit IDs | every digit preserved, no scientific notation, last digits not zeroed out |
| Leading-zero IDs | 007890 keeps its leading zeros |
| Formula-injection safety | =1+1 stays literal text, not evaluated |
| Numeric usability | 99.5 stays a number you can sum and sort, not text |
| Dates | 2026-03-31 unchanged, no timezone shift, no reformatting |

Full comparison with results (two converters that rank for "json to excel online", anonymized, tested 11 June 2026): [Best JSON to Excel converter, 2026 comparison](https://www.jsontoexcel.in/guides/best-json-to-excel-converter)

## Other fixtures

| File | Shape | What to check |
|------|-------|---------------|
| [nested-customers.json](fixtures/nested-customers.json) | nested objects and arrays | address.city becomes its own column |
| [logs.ndjson](fixtures/logs.ndjson) | newline-delimited JSON | one object per line, one row each |
| [api-response.json](fixtures/api-response.json) | data array inside a wrapper | the data array is detected automatically |
| [gstr1-sample.json](fixtures/gstr1-sample.json) | GST GSTR-1 sections | one sheet per section, one row per invoice |

## How to convert these

In Python with pandas:

    import json, pandas as pd
    data = json.load(open("fixtures/nested-customers.json"))
    pd.json_normalize(data).to_excel("out.xlsx", index=False)

Or with no code, drop any file into the free, browser-based [JSON to Excel converter](https://www.jsontoexcel.in/) and download a clean .xlsx file.

## Guides

- [Flatten nested JSON into columns](https://www.jsontoexcel.in/guides/nested-json-to-excel)
- [Convert NDJSON and JSON Lines to Excel](https://www.jsontoexcel.in/guides/ndjson-jsonl-to-excel)
- [Turn an API response into a spreadsheet](https://www.jsontoexcel.in/guides/api-response-to-excel)
- [GST JSON to Excel (GSTR-1, 2A, 2B)](https://www.jsontoexcel.in/guides/gst-json-to-excel)

## License

These test files are released under CC0-1.0 (public domain). Use them in your own tests freely.

Maintained by the team behind [jsontoexcel.in](https://www.jsontoexcel.in/). Disclosure: this repo links to our own free tool.

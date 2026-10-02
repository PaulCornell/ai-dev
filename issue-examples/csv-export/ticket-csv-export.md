# Reports: CSV and PDF export fail with "EXP-504" for large reports (~40k rows)

| | |
|---|---|
| **Type** | Bug |
| **Reported** | 2026-09-29 by Jordan Reyes, Brightwater Logistics |
| **Source** | issue-csv-export.md |

## Summary
Exporting a large report (about 40,000 rows) to CSV spins for a while, then fails with error code `EXP-504`. PDF export of the same report also fails, but Excel export works, and smaller reports export fine. The reporter says it worked last month, before their report grew after they added new warehouses in September.

## Steps to reproduce
**Starting point:** Signed in to an account with a report of about 40,000 rows *(inferred)*, for example the reporter's "All Shipments - Q3."

1. Go to **Reports**.
2. Open the large report (reporter's: "All Shipments - Q3").
3. Click **Export** and choose **CSV**.
4. Wait for the export to finish *(inferred)*.

**How often:** Every attempt, according to the reporter (Chrome and Edge both fail).

## Expected behavior
The CSV file downloads.

## Actual behavior
The export spins for a while, then shows a red error box:

```
Export failed. Something went wrong (code EXP-504)
```

- **PDF** export of the same report also fails (error text not provided).
- **Excel** export of the same report works.
- Smaller reports export to CSV successfully.

## Environment
- **Operating system:** Windows 11 (version not provided)
- **Browser:** Chrome (version not provided); also reproduced in Edge
- **Report size:** about 40,000 rows

## Evidence
- `export-error.png`: screenshot of the error box. Referenced but not attached (see Open questions).

## Additional context
- The reporter says export worked last month (August 2026). The report has grown since they added new warehouses in September, so the change may be data size, a product change, or both.
- The reporter needs the export for a quarterly review on Friday (2026-10-02). Excel export may work as a temporary workaround.

## Possible starting points
These are leads, not a diagnosis:
- The `504` in `EXP-504`, plus the long spin before failing, suggests a timeout (for example, a gateway or request timeout) on large exports.
- Excel export succeeds on the same data, so compare its code path with CSV and PDF. It may stream, paginate, or run asynchronously in a way the others don't.
- Check for any change to export timeouts, limits, or code since about August 2026.

## Definition of fixed
- [ ] Exporting a ~40,000-row report to CSV downloads the complete file without an error.
- [ ] Exporting the same report to PDF succeeds, or PDF is split into its own ticket if the cause turns out to be different.
- [ ] If there's a size limit, users see a clear message about it instead of `EXP-504`.
- [ ] A regression test covers exporting a large report.

## Open questions
- At roughly what row count does export start failing? *(Engineering can likely find this faster than the reporter.)*
- When exactly did it stop working? Did it break when the report grew, or after a product release? *(Reporter or Support)*
- Does the PDF failure show the same `EXP-504` code? *(Reporter)*
- Can the reporter attach `export-error.png`? *(Reporter)*

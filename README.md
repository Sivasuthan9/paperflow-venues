# paperflow-venues

Community-maintained dataset of top conferences and journals for academic CS / AI / ML research, consumed by the [Paperflow](https://github.com/Luxshan2000/paperflow) desktop app.

## Files

- **`manifest.json`** — small file with `schema_version`, `updated_at`, `content_hash` (sha256 of `venues.json`), and `venue_count`. Paperflow fetches this first on every refresh check; if the hash matches the local cache, no further download happens.
- **`venues.json`** — full list of venues. Schema version 1.
- **`taxonomy.md`** — controlled vocabulary for the `categories` field.

## How Paperflow reads this repo

```
https://raw.githubusercontent.com/Sivasuthan9/paperflow-venues/main/manifest.json
https://raw.githubusercontent.com/Sivasuthan9/paperflow-venues/main/venues.json
```

The app caches both locally under `~/.config/paperflow/venues/` and revalidates via the manifest hash. Stale-while-revalidate: it always shows the cached list immediately and refreshes in the background when the user opens the Venues modal (and the local cache is older than 6 hours).

## Updating

1. Edit `venues.json`.
2. Recompute the hash and update `manifest.json`:
   ```bash
   # macOS / Linux
   shasum -a 256 venues.json
   # Windows PowerShell
   Get-FileHash -Algorithm SHA256 venues.json
   ```
   Paste the lowercase hex (prefixed `sha256:`) into `manifest.json`'s `content_hash`. Also bump `updated_at` and `venue_count`.
3. Commit and push to `main`. Paperflow clients pick up the change on next launch / refresh.

## Schema (v1)

Each entry in `venues[]`:

| Field | Type | Notes |
|---|---|---|
| `id` | string | Stable ID, kebab-case. Use `<acronym>-<year>` for conferences, just `<acronym>` for journals. |
| `name` | string | Full title. |
| `acronym` | string | Short name shown on the card. |
| `kind` | `"conference" \| "journal" \| "workshop"` | |
| `tier` | string? | Optional CORE/CCF/custom rank (e.g. `"A*"`). |
| `h_index` | number? | Five-year h-index from Google Scholar Metrics. |
| `categories` | string[] | 1–3 tags from `taxonomy.md`. |
| `description` | string | 1–2 sentences. |
| `homepage` | string | Venue homepage URL. |
| `cfp_url` | string? | Call-for-papers URL. |
| `submission_system` | `"openreview" \| "cmt" \| "hotcrp" \| "easychair" \| "pcs" \| "other"` | |
| `proceedings_publisher` | string? | ACM, IEEE, Springer, PMLR, ACL, etc. |
| `location` | object? | `{ city, country, mode: "in-person" \| "hybrid" \| "virtual" }`. Conferences only. |
| `deadlines` | object | Keyed by kind: `abstract`, `paper`, `rebuttal`, `camera_ready`. Each is `{ date, status }` where `status` is `"confirmed" \| "expected" \| "rolling"`. `date` is ISO-8601 with timezone (or `null` for rolling). |
| `event_dates` | object? | `{ start, end }` ISO dates. Conferences only. |
| `acceptance_rate` | string? | Last year's rate, e.g. `"23%"`. |
| `history` | array | `[{ year, paper_deadline, event_start }]` for at most the last 2 years. Powers the "based on 2025" hint on `expected` deadlines. |

## License

CC0 — public domain dedication. Use freely, attribute if you like.

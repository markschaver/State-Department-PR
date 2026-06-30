# State Department Press Release Archive

Archive of press releases published by the
[U.S. Department of State](https://www.state.gov/press-releases/). Each press release is archived as a JSON file by year and month.

## Files

| Path | Description |
| --- | --- |
| [`data.rss`](data.rss) | The most recent snapshot of the raw RSS feed (overwritten each run). |
| `items/` | The permanent archive — one JSON file per press release, organized by year and month. |
| [`scripts/ingest_rss.py`](scripts/ingest_rss.py) | Parses `data.rss` and writes new items as JSONg. |
| [`.github/workflows/flat.yml`](.github/workflows/flat.yml) | The scheduled GitHub Action that drives everything. |

## Archive

Each press release is stored at:

```
items/<YYYY>/<MM>/<YYYY-MM-DD>-<title-slug>.json
```

For example:

```
items/2026/06/2026-06-29-seychelles-national-day.json
```

Every JSON file contains:

| Field | Description |
| --- | --- |
| `guid` | The feed's unique identifier for the item (used for deduplication). |
| `title` | The press release title. |
| `link` | URL of the full press release on state.gov. |
| `author` | The originating office (e.g. "Office of the Spokesperson"). |
| `published` | Publication timestamp (ISO 8601, UTC). |
| `summary` | The item's `<description>` (HTML). |
| `content` | The item's full `<content:encoded>` body (HTML). |
| `fetched_at` | When this item was archived (ISO 8601, UTC). |

## Running locally

The ingest script has no third-party dependencies and can be run locally.

With a `data.rss` file present, run:

```bash
python3 scripts/ingest_rss.py
```

That will print each newly archived item and a count of how many were added.

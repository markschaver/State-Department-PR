# State Department Press Release Archive

Archive of press releases published by the
[U.S. Department of State](https://www.state.gov/press-releases/). Each press release is archived as a JSON file by year and month.

## Files

| Path | Description |
| --- | --- |
| [`data.rss`](data.rss) | The most recent snapshot of the raw RSS feed (overwritten each run). |
| `items/` | The permanent archive — one JSON file per press release, organized by year and month. |
| [`index.json`](index.json) | A single manifest listing every archived item, regenerated each run. |
| [`scripts/ingest_rss.py`](scripts/ingest_rss.py) | Parses `data.rss` and writes new items as JSONg. |
| [`scripts/build_index.py`](scripts/build_index.py) | Rebuilds `index.json` from the files in `items/`. |
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

## Index manifest

[`index.json`](index.json) is a single file listing every archived item,
sorted newest-first, so a website can load the whole archive in one request
(instead of fetching files individually). It has the shape:

```json
{
  "generated_at": "2026-06-30T18:00:00+00:00",
  "count": 10,
  "items": [
    {
      "guid": "...",
      "title": "...",
      "link": "...",
      "author": "...",
      "published": "...",
      "categories": [],
      "summary": "...",
      "path": "items/2026/06/2026-06-29-seychelles-national-day.json"
    }
  ]
}
```

Each entry's `path` points to the item's full JSON (including the large
`content` field, which is omitted from the manifest to keep it small).

Because this repository is public, the manifest can be fetched directly from a
browser on any domain (GitHub serves it with `Access-Control-Allow-Origin: *`):

```js
const res = await fetch(
  "https://raw.githubusercontent.com/markschaver/State-Department-PR/master/index.json"
);
const { items } = await res.json();
```

## Running locally

The scripts have no third-party dependencies and can be run locally.

With a `data.rss` file present, run:

```bash
python3 scripts/ingest_rss.py   # archive new items
python3 scripts/build_index.py  # rebuild index.json
```

`ingest_rss.py` prints each newly archived item and a count of how many were
added; `build_index.py` writes `index.json` and reports the item count.

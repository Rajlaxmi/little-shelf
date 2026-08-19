# AGENTS.md

Guidance for adding or editing book entries in `index.html`.

## Cover images

Covers are pulled from Open Library: `https://covers.openlibrary.org/b/{key}/{value}-M.jpg`.

- Look up the book first: `https://openlibrary.org/search.json?q=<title>+<author>&fields=title,cover_i,isbn,edition_key`
- **Before using a cover, `curl -s -o /dev/null -w "%{http_code}"` the URL.**
  - `200` — served directly, fast. Use it.
  - `302` — Open Library doesn't have this cover cached; it redirects to an on-the-fly zip extraction on `archive.org`, which reliably adds ~1s of latency per image. Avoid these.
- If the `cover_i` id 302s, try other ISBNs for the same book from the search results (`b/isbn/<isbn>-M.jpg`) — different editions of the same title are often cached separately, and one usually resolves with a direct `200`.
- If every ISBN for a title 302s, there's no fast option on Open Library for that book; leave a note rather than silently shipping a slow cover.

## Book title/detail links

Link the title to a direct product or info page, not a search results page.

- Preferred: a direct Amazon product page (`amazon.com/.../dp/<ASIN>` or `amazon.ca/.../dp/<ASIN>`).
- Fallback: a direct Wikipedia article if one exists for the book, not a `Special:Search` query link — search-fallback links were previously used here and were replaced because they don't reliably land on the right page.

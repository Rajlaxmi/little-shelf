# AGENTS.md

Guidance for adding or editing book entries in `index.html`.

## Cover images

Covers are vendored locally in `dist/covers/` and referenced with a relative `src` (e.g. `dist/covers/raising-good-humans.jpg`) — do **not** point `<img>` tags at `covers.openlibrary.org` directly. Open Library's redirect/extraction path is too slow for a page load (see below), so every cover a book uses must be downloaded once and committed.

To add a cover for a new book:

1. Look up the book: `https://openlibrary.org/search.json?q=<title>+<author>&fields=title,cover_i,isbn,edition_key`
2. Fetch the real image, following redirects, from the `cover_i` id: `curl -sL "https://covers.openlibrary.org/b/id/<cover_i>-M.jpg" -o dist/covers/<slug>.jpg`
3. **Verify it's a real cover, not a placeholder**: `file dist/covers/<slug>.jpg`. A genuine cover is a JPEG of a few KB+ (e.g. `JPEG ... 180x270`). Open Library returns HTTP `200` for a **blank 1x1 GIF placeholder** just as often as for a real image — a `200` status does not mean you got a cover. Any `-isbn/` URL that resolves in a 1x1 GIF is a dead end; ISBNs that redirect (`302`) through `archive.org`'s zip-extraction path are frequently the *only* ones with the real image, so follow the redirect (`curl -sL`) rather than avoiding it — the slow part was only ever fetching it live on every page load, not fetching it once at save time.
4. Reference the saved file with a relative path in `index.html`.

## Book title/detail links

Link the title to a direct product or info page, not a search results page.

- Preferred: a direct Amazon product page (`amazon.com/.../dp/<ASIN>` or `amazon.ca/.../dp/<ASIN>`).
- Fallback: a direct Wikipedia article if one exists for the book, not a `Special:Search` query link — search-fallback links were previously used here and were replaced because they don't reliably land on the right page.

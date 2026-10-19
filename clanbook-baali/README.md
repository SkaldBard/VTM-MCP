# Clanbook: Baali — Searchable Index Bundle

This bundle lets Claude answer questions about this book without
re-reading the original PDF.

## Files

- **`book_index.md`** — thematic index: chapter summaries, themes, key
  terms, cross-cutting arguments. Read this first, in full, for any
  broad/thematic question.
- **`book_chunks.db`** — SQLite database of paragraph-level chunks with
  full-text search, for precise fact lookup, exact figures, or quote
  verification. Query it, don't load it in full.
- **`scripts/query_chunks.py`** — command-line helper for querying the DB.

Book: *Clanbook: Baali* (for *Vampire: The Dark Ages*, 1998) — 70 pages,
indexed 2026-10-19.

Note: this is a **classic Dark Ages**-line sourcebook (1998, original
Storyteller-System Dark Ages rules), not a V20 book. It is included in this
library alongside the V20 books, `v20-dark-ages` and `clanbook-salubri` —
see the top-level `README.md` for how the different game lines/editions in
this library relate to each other, and `library_index.md` for how this
book's account of the Baali compares with the V20 and classic modern-nights
books (including a different "Dark Ages Companion" from this library's
`v20-dark-ages-companion`).

## Three things to know before quoting this bundle

1. **Page numbers differ by one.** `book_index.md` cites the book's
   *printed* page numbers (matching how other bundles in this library are
   indexed), but the `page` column in `book_chunks.db` is the PDF page,
   which is **printed page + 1** (printed p.7 = DB page 8). Cite the printed
   number, or say "DB page."
2. **The source PDF is a scan with no text layer; every chunk is OCR.**
   Body paragraphs are mostly clean but contain residual OCR noise (stray
   commas read as letters, split words, a few garbled words). Decorative
   pages (chapter openers, Table of Contents, the "Eight in Eternity"
   fragment, the Road of the Hive table, the Daimoinon results table) and the
   seven character-sheet pages were checked against the page images and
   hand-corrected; the sheets' dot ratings were counted from the images, not
   OCR'd. For an exact quotation that matters, verify against the PDF. A
   normalization pass also standardized proper names OCR often garbled
   (Namtaru, Mashkan-shapir, D'habi, Azaneal, Iblii-al-Akbar, etc.).
   Searching for a word and finding nothing doesn't prove it's absent —
   try a shorter stem or a nearby word.
3. **Six DB pages have no chunks because they are full-page illustrations**
   (DB pp. 5, 7, 11, 29, 49, 65). A few other pages mix a sidebar into the
   main column, so a paragraph can occasionally be interrupted by sidebar
   text (e.g. DB p.33's "My Kiss" sidebar).

Content note: the book itself carries a "for readers over 18" warning, and
its fiction (Chapter One and the "Endgame" sidebar) contains graphic sexual
violence and torture. The views expressed are the in-character narrators',
not the publisher's.

## For a future Claude session: how to use this bundle

1. Read `book_index.md` in full — it's small and gives you orientation
   plus answers to most broad/thematic questions directly.
2. For precise facts, exact numbers, or verifying a quote, query the
   chunk database instead of guessing from the summary:

   ```bash
   python3 scripts/query_chunks.py book_chunks.db "search terms here"
   python3 scripts/query_chunks.py book_chunks.db --page 42
   python3 scripts/query_chunks.py book_chunks.db --chapter "Chapter Two: Dissonant Echoes"
   ```

   Chapter names used in the DB: `Front Matter`, `Chapter One: The Opening`,
   `Chapter Two: Dissonant Echoes`, `Chapter Three: Descent into Darkness`,
   `Chapter Four: A Hideous Throng`, `Appendix: To Reign in Hell`,
   `Appendix II: Of Interest to Scholars and Chirurgeons`, `Back Cover`.

   Full-text search supports phrase queries (`"exact phrase"`), boolean
   operators (`term1 AND term2`, `term1 NOT term2`), and prefix matching
   (`term*`). Hyphenated names are fragile in FTS5 — search a distinctive
   un-hyphenated word instead (e.g. `Iblii` rather than `Iblii-al-Akbar`,
   `shapir` rather than `Mashkan-shapir`).
3. Cite page numbers from the chunk results when quoting or referencing
   specific facts — this is the audit trail back to the source PDF (see
   point 1 above for the printed-vs-DB offset).
4. Only fall back to the original PDF if both files fail to answer the
   question (e.g. the question is about a figure, chart, or visual
   layout that text extraction wouldn't have captured, or OCR noise makes a
   quotation uncertain).

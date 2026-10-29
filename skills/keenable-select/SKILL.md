---
name: keenable-select
description: "Answer a question across many web pages at once with Keenable SELECT and return a table. Use for lists, comparisons, counts and structured fields (prices, versions, dates, names) that would otherwise take dozens of searches and page reads."
---

# Keenable SELECT

The `select` tool from this plugin answers a question with one read-only SQL
query over live web search results. The server runs the searches, fetches the
pages, extracts the fields the query asks for with an LLM, deduplicates, and
returns rows. The tool's own description is the full guide to the query dialect
(DuckDB `SELECT` with `WEB_SEARCH`, `WEB_FETCH` and the `SEM_*` functions): read
it and follow it. This skill covers when to use the tool and how to work with
the user around it.

## When to use it

- The answer is a table or a list: "which X", "compare X across Y", "how many",
  "every Z with its price / version / date / source".
- The answer is spread over many pages, so one search and a few page reads would
  miss most of it.

For a single fact, one known page, or a quick check, an ordinary search is
faster and cheaper. Use SELECT when the shape of the answer is rows.

## Workflow

1. Settle the columns and the scope with the user when the question is vague:
   which fields, which time range, which region, how many rows.
2. Write one query that casts wide: several genuinely different search phrasings
   in `WEB_SEARCH(...)`, as the tool guide describes, rather than variations of
   one.
3. Tell the user that a query takes from seconds to a few minutes. That is normal
   for a call that searches, reads and extracts; do not cancel it as a hang.
4. Present the table with its sources. Every call returns a result set id: for a
   follow-up on the same data ("only the ones under 30 EUR", "sort by date"),
   query that id in `FROM` instead of searching the web again.
5. Extraction reports what the pages say and does not fact-check them. For
   consequential numbers, cross-check across independent pages and say which
   rows rest on a single source.

## Reports

`generate_html_report` builds a web page from a brief and the result sets you
name, and returns its link. Anyone who has the link can open it. Build one only
when the user asks for a shareable report, and say that before publishing.
`revoke_html_report` takes a report down when the user asks. A report takes
minutes, so build it from aggregated result sets rather than raw tables of
hundreds of rows.

## Account and cost

The server needs a Keenable account. On first use Antigravity opens a browser
sign-in; approve it and the tools appear. Each query is billed to the signed-in
account. If a call fails with 401, the sign-in expired: ask the user to
authenticate the `keenable-select` server again. With 429, wait a moment and
retry once. Never ask the user to paste an API key into chat.

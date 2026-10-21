# Top Words — Specification

## Purpose

Implement a Python 3 function named `top_words` in a module named `top_words.py`. Given an article as text, return the ten most frequent words and their occurrence counts. The function must handle English, Russian, and Kazakh text according to the same rules.

## Interface

- **Signature:** `top_words(text: str) -> list[tuple[str, int]]` (type notation; equivalent implementations are acceptable in Python 3).
- **Input:** One Python `str` containing the article text. Every string is interpreted literally as text, even if it happens to resemble a filename or URL; the function never opens paths or fetches URLs.
- **Output:** A Python list of up to 10 `(word, count)` tuples. Each `word` is a normalized, lowercase-equivalent Python string; each `count` is a positive integer.
- The function returns the list directly. It does not print, read files, fetch URLs, or modify the input. It does not perform language detection.
- Passing a non-string argument raises `TypeError`.

## Exact processing rules

1. **Normalize:** Apply Unicode `casefold()` to the input and then normalize the result using Unicode Normalization Form C (NFC). This gives case-insensitive matching and combines canonically equivalent Unicode spellings when possible. Do not transliterate or translate between languages.
2. **Identify words:** A word is a *maximal consecutive run* of characters `c` for which Python `c.isalpha()` returns `True` in the normalized text. Every character for which `isalpha()` is false is a separator. Separators do not appear in words or count toward frequencies. In particular, whitespace, ASCII apostrophe (`'`), curly quotation apostrophe (`’`), hyphens, digits, underscores, and emoji are separators. **Not every apostrophe-shaped Unicode character is punctuation**: the modifier letter apostrophe (`ʼ`, U+02BC) is alphabetic according to Python and therefore stays within a word. Combining marks not absorbed into an alphabetic character by NFC are separators. A one-letter word counts. Letters from any Unicode script are accepted (including English, Russian, and Kazakh). Adjacent letters from different scripts belong to one word; there is no automatic script-based split.
3. **Count:** Count each occurrence of an identical normalized word. Include common words such as `the` and `и`: there is no stop-word filtering, stemming, or lemmatization.
4. **Sort:** Order word/count pairs by descending count. If counts are equal, order words by ascending Python string lexicographic order (Unicode code-point comparison), **not** by locale-dependent dictionary order.
5. **Limit:** Return the first 10 pairs in that order, or all pairs if there are fewer than 10 unique words. If there are no words, return `[]`.

## Examples and required results

The following expressions are Python strings. Expected results are exact Python values, not printed display strings.

| Behavior tested | Input (`text`) | Expected result of `top_words(text)` |
| --- | --- | --- |
| Frequencies and ordering | `"cat dog cat bird dog cat"` | `[("cat", 3), ("dog", 2), ("bird", 1)]` |
| Case-insensitive matching | `"Cat CAT cat DOG dog"` | `[("cat", 3), ("dog", 2)]` |
| Punctuation, apostrophes, hyphens, digits, underscores | `"red-blue red's red_2blue 123"` | `[("red", 3), ("blue", 2), ("s", 1)]` |
| One-letter words; stop words included | `"I a a the THE"` | `[("a", 2), ("the", 2), ("i", 1)]` |
| Russian and Kazakh Unicode case folding | `"Қала қала ҚАЛА, город ГОРОД."` | `[("қала", 3), ("город", 2)]` |
| Case folding beyond lowercasing (`ß` to `ss`) | `"Straße STRASSE"` | `[("strasse", 2)]` |
| Apostrophe-like Unicode letter vs punctuation | `"don't donʼt"` | `[("don", 1), ("donʼt", 1), ("t", 1)]` |
| Uncomposed combining mark is a separator | `"x\u0301y"` | `[("x", 1), ("y", 1)]` |
| No stemming or lemmatization | `"cat cats cat"` | `[("cat", 2), ("cats", 1)]` |
| No transliteration between writing systems | `"mir мир mir"` | `[("mir", 2), ("мир", 1)]` |
| No script-based word boundary | `"catкот"` | `[("catкот", 1)]` |
| A string resembling a filename is treated as text | `"README.md"` | `[("md", 1), ("readme", 1)]` |
| Unicode canonical equivalence | `"cafe\u0301 café"` | `[("café", 2)]` |
| Tie-break rule independent of first occurrence | `"zoo ant zoo ant"` | `[("ant", 2), ("zoo", 2)]` |
| Ten-word cutoff including ties | `"k j i h g f e d c b a"` | `[("a", 1), ("b", 1), ("c", 1), ("d", 1), ("e", 1), ("f", 1), ("g", 1), ("h", 1), ("i", 1), ("j", 1)]` |
| Whitespace, punctuation and emoji only | `" \n!?🙂42"` | `[]` |
| Empty article | `""` | `[]` |
| Non-string argument | `None` | Raises `TypeError` |

## Acceptance criteria

A conforming implementation must satisfy **all** the rules above and return exactly the expected values for every example. It must not depend on the machine's locale or on external stop-word dictionaries. Unicode character properties and case folding follow the Python 3 interpreter running the function; these tables may differ for newly added Unicode characters across Python versions.

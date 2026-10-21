# Final LLM Review of `spec.md`

**Model:** OpenAI GPT-6 (ChatGPT)

## Exact review prompt

> Review the complete contents of `spec.md` as a specification for the Top Words exercise. Check whether two independent Python 3 implementations could reasonably disagree on any valid input. Look for ambiguous interfaces, Unicode normalization and tokenization problems, case handling, ordering and ties, cutoff rules, edge cases, and contradictions between the rules and the input/output examples. Distinguish actual defects from intentional design decisions. Report remaining blockers and suggested improvements; do not rewrite the specification.

## Review output

**Overall assessment:** The specification is sufficiently precise for two independent implementations using the same Python 3 Unicode definitions to produce equivalent results. No blocking ambiguity or contradiction was found in the 18 examples.

### Findings by area

1. **Interface — no blocker.** `top_words` takes one Python string, returns an ordered list of `(word, count)` tuples, and raises `TypeError` on non-string arguments. The text is explicitly not treated as a filename or URL. Python 3 type notation is descriptive, so equivalent annotations or no runtime annotations are permitted.
2. **Unicode normalization — no blocker.** The order of `casefold()` then NFC normalization is explicit. Implementations should not silently switch to `lower()`, NFKC, transliteration, or repeated normalization.
3. **Tokenization — no blocker.** Maximal runs of characters satisfying Python `str.isalpha()` are precisely defined. The modifier-letter apostrophe U+02BC remains inside a word while ASCII and curly quotation apostrophes split words. Non-composed combining marks are separators. Treating adjacent letters from different scripts as one word is an intentional rule, not a defect.
4. **Case handling — no blocker.** Counting is case-insensitive under Unicode `casefold()`, as shown by the `Straße` example. Case folding may expand one character into multiple characters; this is an intentional part of the definition.
5. **Ordering and ties — no blocker.** Sort by decreasing frequency and then ascending Unicode code-point order. The specification does not rely on insertion order, first occurrence, or locale collation.
6. **Cutoff — no blocker.** Limit to 10 entries after sorting; do not include all words tied at the tenth count.
7. **Edge cases — no blocker.** The requirements cover empty or separator-only text, fewer than 10 unique words, one-letter words, stop words, digits, punctuation, mixed scripts, filenames as text, and invalid argument types.
8. **Examples — no blocker.** The expected values in all 18 rows are consistent with the stated rules. In particular, the punctuation example produces `red` three times and `blue` twice, while the cutoff example selects `a` through `j` among eleven equally frequent tokens.

### Remaining blockers

None identified **for implementations running under the same Python 3 Unicode character definitions**.

### Suggested improvements (optional, not defects)

- **Cross-version reproducibility:** The exact result for newly added Unicode characters could vary between Python releases because `str.isalpha()` and `casefold()` depend on the interpreter's Unicode tables. The specification discloses this. If bit-for-bit agreement across all Python 3 versions is important, pin a Python/Unicode version instead.
- **Additional edge-case example:** Add `"İ"` if you want an explicit demonstration that Unicode case folding can introduce a combining mark (`i` followed by a combining dot), which is then treated as a separator if it remains uncomposed. Existing rules already determine this outcome.

**Conclusion:** No mandatory change to `spec.md` is needed as a result of this review.

# Walsall Council: what we heard and what we changed

Prepared 5 November 2026 for Walsall Council. It covers the scans of
`go.walsall.gov.uk` in the Vital Core accessibility reports, and the feedback
from Walsall's accessibility lead.

Figures come from the report published on 4 November 2026 (week 2026-W45).
Scans were still running that week, so numbers will keep moving. See
[What will take time](#what-will-take-time).

Live report: <https://mgifford.github.io/vital-core-lgd/reports/go.walsall.gov.uk/2026-W45/accessible.html>
(the new "Scope of analysis" section is part of that page).

## What we heard

1. One finding was reported across 73 URLs, but those URLs are variations of
   one feedback form (rating Good, Ok or Poor, plus a parent-page parameter).
2. Five rules were reported across 292 URLs, all tied to one third-party
   consent-management component (`qc-cmp2` CSS classes).
3. The report makes repeated observations look like a large number of separate
   defects that could each be fixed on their own.
4. The consent component's failures dominate the report and make Walsall's own
   page problems harder to see.
5. Manual and user testing did not find the reported Honeypot issue, although
   Walsall accepts that other improvements are needed.
6. Four pairs of results from the two scanning tools (axe and Alfa) were
   questioned as possible duplicates.
7. Readers should be able to choose the scope of what they look at (the whole
   page, only Walsall's own content, or only the shared component) without
   changing the underlying scan.

Our guiding principle, taken from that feedback: **count every observed
failure, but count something as a separate fixable defect only when the evidence
supports it.**

## What we found when we checked

- **The 73 and 292 are real, but partial.** They are the totals partway through
  the week's scanning (after 6 of the first 12 runs). When we first checked, the
  same week held 597 URLs, with 132 feedback-form URLs. By the 4 November report
  it was 994 URLs and 217 feedback-form URLs, and scanning was still going.
- **The feedback form is one implementation.** The Good, Ok and Poor versions
  differ only in the form's web address, which radio button is pre-selected, and
  security tokens that change on every request.
- **The consent dialog accounts for a lot, but not everything.** Seven of the 31
  findings sit entirely inside it: five axe rules (buttons without names, list
  structure, two ARIA parent/child rules, a role rule) and two Alfa rules. We
  confirmed this using several independent signs (the element's CSS class, where
  it sits in the page, and scripts loaded from `inmobi.com` and
  `quantserve.com`), not only the `qc-cmp2` name.
- **Some findings are Walsall's own.** Low-contrast text is a mix: about
  three-quarters of the examples are inside the consent dialog and the rest are
  not. Findings such as missing page regions (the skip link, phase banner and
  hero), heading order and others are on Walsall's own templates. They were
  being hidden by the consent noise.
- **The Honeypot finding is one hidden form field.** An attribute on a wrapper
  around the hidden field is flagged on every feedback page. That fits Walsall's
  testing, since users cannot see that field. We could not tell which module or
  theme produces it.
- **The four tool pairs are related, not simple duplicates.** Only a match on
  both the same rule definition and the same element counts as a duplicate. On
  the data we checked, none of the four pairs was confirmed as a duplicate
  across the whole site. Two are plainly related findings, one is uncertain, and
  one matched on a sampled element but is kept separate at site level.
- **The scanner and Walsall's residents may see different consent dialogs.**
  Our scanner saw a dialog with the buttons "Close", "CONFIRM", "Google
  consents" and "Personal Data Processing Opt Out" on every page it recorded
  (342 pages at the time of writing).
  When we looked from a location outside the US we saw a different dialog
  ("Agree", "Disagree", "More options"). Our scans run from cloud servers that
  are probably not in the UK. This is not confirmed, but if it is right, the
  consent findings describe a dialog most Walsall residents never see.

## What we changed

| What we heard | What changed |
|---|---|
| Many URLs look like many defects | A new **Scope of analysis** section reports how many distinct URLs were scanned, and how many are known to be the same page implementation. For the feedback form, 217 URLs are counted as 1 declared implementation, with the three rating states shown. Each URL is still kept and counted. |
| The consent dialog dominates | Findings inside it are now grouped under **Shared-component findings**, with the evidence and our confidence shown. Each different failure stays its own finding: grouping never merges different rules. |
| Walsall's own problems are hard to see | A **Page-specific findings** view leaves out only findings confirmed to be in the shared component. It is labelled as a diagnostic view, not a complete accessibility assessment. The three views are plain sections of the page and work without JavaScript; an optional filter (which needs JavaScript) switches between them. |
| The action list is all consent | **Next 10 actions** marks consent actions with a "Shared component" label and adds a second list of the top actions outside it. **Training priorities** notes how many rules are shared-component findings. |
| Readers cannot see the picture quickly | A short summary opens the Accessibility page and the report's landing page: URLs scanned, page families, and how many findings are in a verified shared component. |
| Findings on the feedback form | A finding seen only on the feedback form now says so (for example the Honeypot finding: all 217 of its URLs are one declared implementation). |
| Four tool pairs questioned | Each overlap is now labelled Confirmed duplicate, Related, Uncertain or Independent, with its evidence. Both tools' original results are kept. The report says the number of fixes is unknown, because we cannot prove it. |
| What did the scanner actually see? | For Walsall's consent dialog the scanner now records, best effort and without clicking anything, whether it was present, visible, shown as a dialog, and its button labels, and which browser identity was used. |
| Report text error | A wording bug that printed "31ot not attributed" is fixed. |

What did **not** change: the underlying scan results, page identity, finding
identifiers and history are untouched, and the headline score is calculated as
before. Nothing was removed or hidden. Everything we added is additive.

## What will take time

Some of this cannot be finished in one report. Scans add pages through the week
and the new evidence is only recorded for pages scanned after the change.

- **Counts will settle gradually.** Some pages were scanned before the new
  evidence existed. Expect the shared-component and page-specific figures to
  keep shifting through week 2026-W45 and to settle in the full 2026-W46 report.
- **Earlier weeks keep their original numbers.** History is kept as it was, so
  trends are not rewritten.
- **Attribution is based on samples.** The scanner stores a few examples per
  rule per page. It labels what it saw and does not inspect every instance.
- **Alfa results cannot be traced to a single element.** Four Alfa findings
  will stay labelled "shared component and other elements" until that changes.
- **Who owns a fix is not claimed.** We do not yet say whether a finding is a
  third-party, theme or Drupal-distribution problem.
- **The report is rebuilt on a schedule.** The published page can lag the
  newest scan data by several hours.
- **Automated testing finds only part of the barriers.** Manual testing with
  assistive technology is still needed. We have not yet had the new report
  section tested by screen reader users.

## What would help from Walsall

1. **Consent dialog:** which dialog do residents see in the UK, and is it set
   by country? A screenshot of a first visit from a UK address would settle
   whether our scanner sees something different.
2. **Honeypot field:** which Drupal module or theme adds the hidden field, so
   the finding can go to the right team.
3. **Consent classes:** confirmation that the generated `css-` class names in
   the dialog belong to the same consent tool, so more of its findings can be
   attributed with confidence.
4. **Views:** whether the Page-specific view should be the first thing your team
   sees, with the full-page view one click away.

## Technical references

For your developers. Shared code changes in
[Vital Core](https://github.com/mgifford/vital-core): pull requests #292
(cross-engine correlations and reporting), #293 (shared components and page
families), #294 (component observation) and #295 (component-aware action queue
and text fix). Walsall settings and the written record are in
[vital-core-lgd](https://github.com/mgifford/vital-core-lgd): pull requests #16
to #20 and `docs-internal/UPSTREAM-SUGGESTIONS.md` (items 24 to 27). Walsall's
shared-component settings are in `config/targets.yml` under `go.walsall.gov.uk`.

# Upstream suggestions for vital-core

Things found while running the LocalGov Drupal fleet that belong in the
shared engine, [mgifford/vital-core](https://github.com/mgifford/vital-core),
rather than in this repo's config. vital-core never ships this file, so it
never conflicts when merging upstream.

Before adding one, check
[vital-core-nsf's list](https://github.com/mgifford/vital-core-nsf/blob/main/docs-internal/UPSTREAM-SUGGESTIONS.md)
and vital-core's open issues and PRs, and link instead of repeating.

## How to use this file

- **Add** a numbered section with what was seen, where (run, report URL or
  `file:line`), and a suggested fix. Numbers are never reused, so links to
  `#3` stay valid.
- **Fix upstream, not here.** Make the change on a branch from
  `upstream/main` and open the PR on vital-core. After it merges, run
  `git fetch upstream main && git merge upstream/main` here. If this fleet
  can't wait, merge the same branch here too. Never copy the change across
  by hand: copied changes conflict on the next merge.
- **Keep the status current**, in the table and the section:
  `open` → `issue <link>` or `PR <link>` → `merged upstream <link>` →
  `merged here <date>`, or `dropped` with the reason.
- Fleet-only changes (`config/targets.yml`, `config/profiles/`, the README
  introduction) don't go here.

## Status

| # | Suggestion | Status |
|---|---|---|
| 1 | Spell check is US English only | merged upstream [vital-core#281](https://github.com/mgifford/vital-core/pull/281); merged here 2026-10-28 (`spelling_locale: en-GB`) |
| 2 | `.gov.uk` fails the government-domain check | merged upstream [vital-core#281](https://github.com/mgifford/vital-core/pull/281); merged here 2026-10-28 |
| 3 | Applying a profile needs a workflow edit | open (same as vital-core-nsf §8) |
| 4 | README said not to use "Use this template" | merged upstream [vital-core#279](https://github.com/mgifford/vital-core/pull/279); merged here 2026-10-27 |
| 5 | Shipped defaults assume a US federal fleet | dropped: vital-core is the CMS fleet, so its US defaults are correct there |
| 6 | Dependabot can't fix the `@vitest/mocker` advisory | merged upstream [vital-core#280](https://github.com/mgifford/vital-core/pull/280); merged here 2026-10-27 |
| 7 | Wappalyzer fingerprints are stale | merged upstream [vital-core#280](https://github.com/mgifford/vital-core/pull/280); merged here 2026-10-27 |
| 8 | A Lighthouse race crashes the whole scan | merged upstream [vital-core#280](https://github.com/mgifford/vital-core/pull/280); merged here 2026-10-27 |
| 9 | One failed domain skips the optional report for all | merged upstream [vital-core#281](https://github.com/mgifford/vital-core/pull/281); merged here 2026-10-28 |

## 1. Spell check is US English only

Recorded 2026-10-27. **Status: merged upstream in
[vital-core#281](https://github.com/mgifford/vital-core/pull/281); merged
here 2026-10-28.**
This fleet sets `spelling_locale: en-GB` in `config/targets.yml`.

`src/lib/spell.js:4` loads `dictionary-en`, which is US English. Councils
write British English, so words like "organisation", "colour", "centre" and
"programme" are reported as misspellings on every page. The plain-language
engine uses the same speller (`src/engines/plain-language.js:99`), so its
results are skewed too. Canadian English differs again from both.

Suggested fix: a `spelling_locale` setting, global and per target, defaulting
to `en-US` so existing reports don't change. `dictionary-en-gb`,
`dictionary-en-ca` and `dictionary-en-au` (3.0.0) are available under the
same licence as `dictionary-en`. Cache one speller per locale. This fleet
would then set `spelling_locale: en-GB`.

## 2. `.gov.uk` fails the government-domain check

Recorded 2026-10-27. **Status: merged upstream in
[vital-core#281](https://github.com/mgifford/vital-core/pull/281); merged
here 2026-10-28.**

`src/engines/security.js:16` checks the host against
`['.gov', '.mil', '.edu', '.fed.us']`, labelled "Sponsored government TLD
(.gov/.mil/.edu)". Every council in this fleet fails it, which lowers its
security pass rate for a domain that is in fact government-only.

Suggested fix: recognise national government domains such as `.gov.uk`,
`.gov.au` and `.govt.nz` (`src/lib/urls.js` already lists these as
multi-label suffixes), and relabel the check "Government domain".

## 3. Applying a profile needs a workflow edit

Recorded 2026-10-27. **Status: open**, same as vital-core-nsf §8.

The only way to apply `config/profiles/lgd.yml` is to set `VITAL_PROFILE: lgd`
on the Aggregate step of `.github/workflows/report.yml`
([vital-core-lgd#2](https://github.com/mgifford/vital-core-lgd/pull/2)).
That edit can conflict when upstream changes the same lines, and it goes
against the profile README's rule that profiles shouldn't need workflow
edits.

Suggested fix: `VITAL_PROFILE: ${{ vars.VITAL_PROFILE }}` in upstream's
`report.yml`, so a fleet sets it under Settings → Variables.

## 4. README said not to use "Use this template"

Recorded 2026-10-27. **Status: merged upstream in
[vital-core#279](https://github.com/mgifford/vital-core/pull/279); merged
here 2026-10-27.**

vital-core is a GitHub template, and this repo was made with "Use this
template". But the README told people not to use it, without saying how to
join the history afterwards. #279 makes the template the documented path,
adds the one-time join, and sets up this file's convention for every fleet.
This repo's join is in
[vital-core-lgd#2](https://github.com/mgifford/vital-core-lgd/pull/2).

## 5. Shipped defaults assume a US federal fleet

Recorded 2026-10-27. **Status: dropped** (2026-10-28).

vital-core is itself the CMS fleet, and every CMS site is in the US, so its
`config/targets.yml`, Section 508 baseline, `en-US` spelling and
`AGENTS.md` mission are correct for vital-core. A fleet made from the
template replaces `targets.yml` anyway (README "Setting up a new
instance", step 3). The original note is kept below for the record.

Setting up this fleet meant changing almost every global value in
`config/targets.yml`:

- the CMS, HHS and VA targets
- `data_repo`, `report_base_url` and the `user_agent` contact
- the Section 508 `legal_baseline`
- `languages: [en, fr, ja, nl]`
- budgets of 150 pages per run and 10,000 per week

`AGENTS.md` also describes the mission as tracking "US government sites",
with "WCAG 2.0 AA for federal, WCAG 2.1 AA for state/local".

Suggested fix: ship the engine defaults separately from a fleet's targets,
for example as a `config/targets.example.yml`, as vital-core-nsf §3
suggests. Also word the `AGENTS.md` mission so it fits any government,
with the legal baseline left to each fleet's config.

## 6. Dependabot can't fix the `@vitest/mocker` advisory

Recorded 2026-10-27. **Status: merged upstream in
[vital-core#280](https://github.com/mgifford/vital-core/pull/280); merged
here 2026-10-27.**

This repo's first Dependabot security run
([40595139585](https://github.com/mgifford/vital-core-lgd/actions/runs/40595139585))
failed with "security update not possible" for `@vitest/mocker` 4.1.11
(fixed in 4.1.12). `vitest` pins `@vitest/mocker` exactly, and `vitest` is a
production dependency through `@siteimprove/alfa-test-utils` →
`@siteimprove/alfa-test` (`vitest@^4.1.8`), so the fix is to move `vitest`
itself. The lockfile is shared with every fleet, so #280 does it upstream; it
also moves `fast-uri` to 3.1.9, which this repo already has from its
Dependabot PR #1 (the later merge is clean).

## 7. Wappalyzer fingerprints are stale

Recorded 2026-10-27. **Status: merged upstream in
[vital-core#280](https://github.com/mgifford/vital-core/pull/280); merged
here 2026-10-27.**

Scans here warn "Wappalyzer fingerprints are 134 days old (vendored
2026-06-15). Run scripts/update-wappalyzer.sh to refresh." `vendor/wappalyzer/`
is shared with every fleet, so the refresh belongs upstream. The script needs
an authenticated `gh`; nothing refreshes it on a schedule.

## 8. A Lighthouse race crashes the whole scan

Recorded 2026-10-27. **Status: merged upstream in
[vital-core#280](https://github.com/mgifford/vital-core/pull/280); merged
here 2026-10-27.**

The first `go.walsall.gov.uk` scan
([job](https://github.com/mgifford/vital-core-lgd/actions/runs/40596432079/job/122513577156))
exited with "Protocol error (Target.getTargetInfo): Session with given id not
found" from Lighthouse's `target-manager.js`. Lighthouse ignores "Target
closed" when a page removes an iframe or worker before it finishes attaching,
but rethrows this wording of the same race from an event listener, so it is
an unhandled rejection and Node exits. The other three councils were fine,
and Walsall's pages scanned before the crash were committed. Still present in
lighthouse 13.6.0. #280 ignores exactly that error while Lighthouse runs and
still crashes on anything else.

Also worth reporting to Lighthouse (GoogleChrome/lighthouse), so the guard can
be removed once a release treats it like "Target closed".

## 9. One failed domain skips the optional report for all

Recorded 2026-10-27. **Status: merged upstream in
[vital-core#281](https://github.com/mgifford/vital-core/pull/281); merged
here 2026-10-28.**

In the same run, the `optional-report` job was skipped because it requires
`needs.scan.result == 'success'`, and Walsall's job had failed. Each domain's
data is committed on its own, so the three councils that worked could have
been reported. Suggested fix in upstream `scan.yml`: also accept
`needs.scan.result == 'failure'` (not `cancelled`) for the optional report.

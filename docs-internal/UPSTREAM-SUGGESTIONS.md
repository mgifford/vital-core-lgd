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
| 1 | Spell check is US English only | open |
| 2 | `.gov.uk` fails the government-domain check | open |
| 3 | Applying a profile needs a workflow edit | open (same as vital-core-nsf §8) |
| 4 | README said not to use "Use this template" | PR [vital-core#279](https://github.com/mgifford/vital-core/pull/279) |
| 5 | Shipped defaults assume a US federal fleet | open (related: vital-core-nsf §2, §3) |

## 1. Spell check is US English only

Recorded 2026-10-27. **Status: open.**

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

Recorded 2026-10-27. **Status: open.**

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

Recorded 2026-10-27. **Status: PR
[vital-core#279](https://github.com/mgifford/vital-core/pull/279).**

vital-core is a GitHub template, and this repo was made with "Use this
template". But the README told people not to use it, without saying how to
join the history afterwards. #279 makes the template the documented path,
adds the one-time join, and sets up this file's convention for every fleet.
This repo's join is in
[vital-core-lgd#2](https://github.com/mgifford/vital-core-lgd/pull/2).

## 5. Shipped defaults assume a US federal fleet

Recorded 2026-10-27. **Status: open**, related to vital-core-nsf §2 and §3.

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

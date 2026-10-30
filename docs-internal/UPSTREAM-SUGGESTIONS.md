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
| 10 | A homepage redirect to another subdomain stops the crawl | open; [vital-core#283](https://github.com/mgifford/vital-core/pull/283) handles apex/`www.` and `http`/`https` redirects, not another subdomain |
| 11 | A page that fails three times is never retried | merged upstream [vital-core#283](https://github.com/mgifford/vital-core/pull/283) (retried once a week); merged here 2026-10-29 |
| 12 | No way to see why a site won't load for the scanner | merged upstream [vital-core#282](https://github.com/mgifford/vital-core/pull/282) (`diagnose` workflow); merged here 2026-10-29 |
| 13 | Renaming a target's domain drops its history from the report | open |
| 14 | `http://` sitemap URLs are scanned as separate pages | merged upstream [vital-core#283](https://github.com/mgifford/vital-core/pull/283) (canonical URLs); merged here 2026-10-29 |
| 15 | A blocked site is left off the dashboard, or scored on its firewall's page | merged upstream [vital-core#283](https://github.com/mgifford/vital-core/pull/283) (blocked-sites notice); merged here 2026-10-29 |
| 16 | `WAF-ALLOWLIST.md` keeps a CMS-only "Currently blocked" table in a shared doc | open |

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

## 10. A homepage redirect to another subdomain stops the crawl

Recorded 2026-10-28. **Status: open.** [vital-core#283](https://github.com/mgifford/vital-core/pull/283) resolves each site's canonical
origin from its homepage redirect, but only within the same site (apex and
`www.`, `http` and `https`); a redirect to another subdomain still keeps the
configured origin and is only logged.

The crawler keeps a link only when its host matches `target.domain`, with a
leading `www.` ignored (`normalizeUrl` in `src/lib/urls.js`; `src/scan.js`
around line 388). Walsall serves its pages and links from
`go.walsall.gov.uk`, so a target of `walsall.gov.uk` that redirects there
would drop every link and scan only the homepage. This fleet therefore keeps
`go.walsall.gov.uk`. Other councils may use a similar subdomain.
The diagnose run on 2026-10-29 confirmed it: `walsall.gov.uk` and
`www.walsall.gov.uk` both answer 301 to `go.walsall.gov.uk`.

Suggested fix: when the homepage (or the first seed) redirects to a
different host on the same registrable domain, log it and scope links to the
final host, or fail the config check with a message saying which host to
use.

## 11. A page that fails three times is never retried

Recorded 2026-10-28. **Status: merged upstream in [vital-core#283](https://github.com/mgifford/vital-core/pull/283); merged here
2026-10-29.** A page that
failed three times is tried once more each week (`lastFailedWeek`).

`www.blackburn.gov.uk` timed out on its homepage in all three first-week
runs (30 s `page.goto` timeouts; 0 pages recorded). Each failure increments
`failCount`, which only resets on a successful scan (`src/scan.js` around
line 426), but `pickBatch()` and `budgetStatus()` skip pages with
`failCount >= 3` (`src/lib/state.js`). So the homepage is never tried
again, the domain's frontier counts as empty, and the scheduled matrix
skips the domain from then on. A site that is down for three runs in a row
drops out for good. This fleet sidestepped it by renaming the target to
`blackburn.gov.uk` (a new key, so fresh crawl state) and raising its
`nav_timeout_ms` to 60000.

Suggested fix: let failed pages be retried after a cool-off, for example
once per ISO week, or reset `failCount` when a new week starts, so a
temporary outage costs a week's coverage at most.

## 12. No way to see why a site won't load for the scanner

Recorded 2026-10-28. **Status: merged upstream in
[vital-core#282](https://github.com/mgifford/vital-core/pull/282); merged
here 2026-10-29.**

The first week's scans recorded only a status code or an error per page:
`www.lbhf.gov.uk` gave 202s and 404s, and `www.blackburn.gov.uk` timed out.
Nothing showed the redirect chain, the headers that identify a firewall or
CDN, or what a slow page was still loading, and the development sandbox
can't reach the sites. vital-core#282 adds a manual `diagnose` workflow
(`scripts/probe-targets.js`) that reports all of these from GitHub's
network. Run it (Actions → diagnose) before asking a council to allowlist
the scanner.

## 13. Renaming a target's domain drops its history from the report

Recorded 2026-10-29. **Status: open.**

`domainKey()` uses the configured domain verbatim, and the data directory,
the crawl-state asset and the report path all hang off that key. On
2026-10-28 this fleet renamed `www.hackney.gov.uk` to `hackney.gov.uk` and
`www.lbhf.gov.uk` to `lbhf.gov.uk`, although both apexes only redirect to
`www.`. Hackney's 498 pages from that week dropped out of the report, its
crawl restarted at 50 pages, and the next report listed one council of five.
Nothing warned about it. This fleet reverted both names on 2026-10-29.

Suggested fix: a `previous_domains:` list per target that aggregate and the
crawl-state restore read as aliases, or at least a config check that warns
when a target has no data directory but a `www.`-equivalent one does.

## 14. `http://` sitemap URLs are scanned as separate pages

Recorded 2026-10-29. **Status: merged upstream in [vital-core#283](https://github.com/mgifford/vital-core/pull/283); merged here
2026-10-29.**

Walsall's sitemap lists `http://go.walsall.gov.uk/…` URLs, and every one
redirects to `https://`. `normalizeUrl()` kept the scheme as written, so 453
of the 474 pages scanned in 2026-W44 were stored under `http://`, 157 pages
were queued under both schemes, and every page cost a redirect. The URL
lookup did a plain text match, so pasting the `https://` address of
`/events/junior-paint-master-workshop-rob-conway-morning` found nothing.
The "Served over HTTPS" check also always passed, because the origin was
assumed to be `https://`. The PR records every same-site URL under the
scheme and host the homepage redirects to, merges old duplicates, makes the
lookup ignore scheme and `www.`, and fails the HTTPS check for HTTP-only
sites.

## 15. A blocked site is left off the dashboard, or scored on its firewall's page

Recorded 2026-10-29. **Status: merged upstream in [vital-core#283](https://github.com/mgifford/vital-core/pull/283); merged here
2026-10-29.**

After the domain fixes the dashboard showed four councils of five, with no
word about Blackburn: GitHub's network can't connect to it (diagnose run,
2026-10-29), and the dashboard only lists targets with an audited page.
LBHF showed "B 80, 2 pages audited", but both pages were its AWS WAF
challenge page (HTTP 202), which the scan audited as if it were the site;
its other 39 pages answered 404. The existing "Blocked targets" list was
collapsed at the bottom and only caught all-error sites. The PR records
challenge and refusal pages as blocked (no audit), gives each target an
access status, and lists blocked, partly blocked and unreachable sites in a
closed `<details>` under the dashboard intro whose summary names them, with
the cause and what to ask the council for.

## 16. `WAF-ALLOWLIST.md` keeps a CMS-only "Currently blocked" table in a shared doc

Recorded 2026-10-29. **Status: open.**

The doc's "Currently blocked" table (www.hhs.gov, www.nih.gov), request
template ("public U.S. government websites") and "Where to send it" section
describe the CMS fleet, but the file is shared by every fleet, so this fleet
can't list LBHF or Blackburn there without conflicting on the next upstream
merge. With the dashboard now listing blocked sites and their causes, the
table could go, and the template could take the fleet's name and country
from config.

## 17. "Government domain" said "uk" fails a `.gov.uk` council

Recorded 2026-10-30. **Status: merged upstream in
[vital-core#286](https://github.com/mgifford/vital-core/pull/286); merged here
2026-10-30.**

[Walsall's security page](https://mgifford.github.io/vital-core-lgd/reports/go.walsall.gov.uk/2026-W44/security.html)
showed "✗ Sponsored government TLD (.gov/.mil/.edu) uk". `.gov.uk` has
counted since vital-core#281, but each security check is stored with the
scan that ran it, so a result from before then kept the old label and the
fail. The PR re-derives the check from the domain at build time, names the
country ("gov.uk (United Kingdom)"), and adds a `government_domains`
setting for namespaces the built-in list lacks.

## 18. "Resolves with and without www" checked www.go.walsall.gov.uk

Recorded 2026-10-30. **Status: merged upstream in
[vital-core#286](https://github.com/mgifford/vital-core/pull/286); merged here
2026-10-30.**

The check prefixed `www.` to the scanned host, so for Walsall it fetched
`www.go.walsall.gov.uk`, which nobody types, and failed. What matters is
whether `walsall.gov.uk` and `www.walsall.gov.uk` answer, by redirecting to
`go.` or otherwise, rather than with an error. The PR checks the registrable
domain with and without `www.` (Public Suffix List) and says where each
landed. Walsall's result updates at its next scan.

## 19. Tech ↔ issues listed 1.02× pairs, with no way to see the finding

Recorded 2026-10-30. **Status: merged upstream in
[vital-core#286](https://github.com/mgifford/vital-core/pull/286); merged here
2026-10-30.**

[Walsall's tech ↔ issues page](https://mgifford.github.io/vital-core-lgd/reports/go.walsall.gov.uk/2026-W44/tech-findings.html)
listed "aria-prohibited-attr (axe) 1.02×" for Google Ads, which runs on 59
of 60 pages: lift against all pages is about 1 for any finding there. Eleven
Alfa rules showed the same "26 / 47": Alfa runs on half of this fleet's
pages, but every page with tech data was counted, so findings on every page
Alfa checked looked tied to whatever technology the sample favoured. No row
linked to the finding. The PR compares pages with and without each
technology per engine, lists pairs at least 1.5 times as common, names the
technologies on nearly every page as not compared, and links each finding
to its Accessibility report. On this fleet's W44 data no pair reaches 1.5×:
the failures are on nearly every page either way, so they come from shared
templates.

## 20. Dependabot: `@hono/node-server` serveStatic double decode

Recorded 2026-10-30. **Status: merged upstream in
[vital-core#287](https://github.com/mgifford/vital-core/pull/287); merged here
2026-10-30.**

[Dependabot alert 7](https://github.com/mgifford/vital-core-lgd/security/dependabot/7)
flagged `@hono/node-server` 2.0.11 (patched in 2.1.3), which arrives through
`@modelcontextprotocol/sdk`; Dependabot timed out creating the update. The
lockfile is shared by every fleet, so the bump went upstream. The MCP server
uses stdio and serves no static files, so it was not exposed.

## 21. Check the PDFs the councils link to

Recorded 2026-10-30. **Status: merged upstream in
[vital-core#288](https://github.com/mgifford/vital-core/pull/288) and
[#289](https://github.com/mgifford/vital-core/pull/289) (with
[pdf-crawler#296](https://github.com/mgifford/pdf-crawler/pull/296)); merged
here and enabled 2026-10-30.**

The scans list 719 PDFs across this fleet's councils (306 Walsall, 357
Luton, 56 Hackney), but nothing checked them. The PR adds a `pdf-audit`
workflow that checks each site's own-domain PDFs with pdf-crawler, 25 per
site a day, plus manual runs for one site, and a PDFs page per domain. This
fleet sets `pdf_audit: { enabled: true }` in its defaults.

## 22. `pdf-audit` warned that `setup-uv@v6` runs on Node 20

Recorded 2026-10-30. **Status: merged upstream in
[vital-core#290](https://github.com/mgifford/vital-core/pull/290); merged
here 2026-10-30.**

This fleet's first `pdf-audit` run
([41038668893](https://github.com/mgifford/vital-core-lgd/actions/runs/41038668893))
worked but warned that `astral-sh/setup-uv@v6` targets Node 20, which
GitHub now forces onto Node 24. The workflow is shared, so the fix went
upstream: setup-uv v10.3.3 (Node 24), pinned by SHA because its floating
tags stop at v7. pdf-crawler's own workflows got the same treatment in
[pdf-crawler#298](https://github.com/mgifford/pdf-crawler/pull/298).

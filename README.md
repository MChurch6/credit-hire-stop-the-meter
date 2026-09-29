# Credit Hire: Stop the Meter

A playable single-player claims-training prototype based on the supplied **Gamify Claims Learning** module. No installation, database, API key or paid service is needed to run locally.

## Start

1. Install Node.js 20 or newer if it is not already available.
2. Open a terminal in this project folder.
3. Run `npm start` (or `node server.mjs`).
4. Open **http://127.0.0.1:4173** in a modern browser.

Do not double-click `dist/index.html`: the browser needs HTTP to load the scenario and modules. If port 4173 is busy, PowerShell users can run `$env:PORT=4174` followed by `npm start`.

The app is static; `dist/` can also be served by a standard static web host. There is no build step and no package installation. Google Fonts is optional; local system fonts are used when offline. The complete game works without an internet connection once served locally.

## Play

Open evidence, commit an action and read the debrief. Ten decisions cover liability/first response, need, type, intervention, repair enquiries, authority delay, financial evidence, BHR, issue allocation and a final 30–150 word strategy note. Choices change delay, evidence and the coaching outcome. Use the field guide at any point.

The five handling scores start at 50, are clamped to 0–100, and total 500. XP is independent, with a maximum of 450. Five competence badges and three endings reward balanced handling. The report includes the complete decision history, exposure breakdown, coaching priorities, model strategy, report download and print-to-PDF.

The hire meter advances only on committed decisions. It never penalises reading time. The reference route takes 27 calendar days at £295 (£7,965 hire, £8,355 including claimed extras). Diarying the authority review for another week adds 7 days / £2,065. The initial 18-working-day engineer estimate is provisional, not an assertion that actual completion must take that long. The £72 intervention offer remains declined pending advice in this scenario: it does not automatically stop or cap the meter.

## Save and replay

Progress and the current scenario are stored only in this browser's local storage. There are no learner accounts, shared analytics or server records. Refresh resumes the committed attempt and note draft; uncommitted choices can be reselected. Restart asks for confirmation. Download the coaching report before replacing a completed attempt. Different browser origins have independent saves. Clearing browser storage removes saved attempts.

## Scenario authoring

Open **Scenario studio**. Edit title/rate or the full JSON; import an edited JSON file; validate and apply to begin a fresh attempt. Export the scenario to share it. **Restore original scenario** restores the shipped file after confirmation. Changes made in the browser do not overwrite project files; copy an exported scenario into `dist/scenario.json` to change the default for everyone.

The top title/rate fields override those JSON fields when applying/exporting. If changing amounts, update amounts embedded in question and document text as well. Do not enter real customer or financial information.

Content is separated from the interface and scoring engine:

| File | Purpose |
|---|---|
| `dist/scenario.json` | Default claim, artefacts, branching flags, decisions, feedback and rubric |
| `dist/engine.js` | Pure validation, transition, scoring, badges and outcome functions |
| `dist/app.js` | Claims desktop, evidence, timeline, editor, browser saving and report |
| `dist/styles.css` | Responsive desktop/mobile theme and print layout |
| `server.mjs` | Dependency-free local static server, bound to loopback |
| `tests/engine.test.mjs` | Complete-route, branching, validation and scoring checks |

### Content schema

`schemaVersion: 1`; `id`, `title`, `claimRef`, `claimant`, `vehicle`, positive `dailyRate`; arrays `artifacts` and `stages`.

Each artefact has `id`, `title`, `kind`, `from`, a zero-based `unlock` stage and plain-text `body`. Optional `variants` have `{flag, body}`; the first matching earned flag changes the content. Placeholders `{{day}}`, `{{hire}}`, `{{total}}` show the evolving invoice.

Each stage has `id`, `phase`, `title`, `prompt`, `type`, `days`, `xp`, and `docs` (artefact IDs). Supported types:

- `choice`: options carry `id`, `label`, a five-value `score` array, `quality` from 0–1, `feedback`, optional `extraDays` and `flags`.
- `multi`: `options`, `correct` IDs, `feedback`, optional competence `flag` and `missDelay`. Quality is (correct selected minus incorrect selected) / number correct, floored at zero.
- `allocation`: `issues` carry `id`, `label` and `answer` (`Accept`, `Challenge`, `Investigate`). Quality is the proportion matched.
- `note`: `rubric` topics have `id`, `label`, `terms`; matches award coverage XP. The note must contain 30–150 words.

The current badges and three outcome rules are credit-hire specific and intentionally live in `engine.js`; adapt them for other subjects. The credit-hire shell and £120/£270 extras are also module defaults. Reusing the engine for other training subjects needs those presentation and outcome labels adapted. Stage ordering is linear, with stateful branches in consequences, evidence and final outcome; this is not a visual branching-tree editor.

## Validation

Run `npm test` for meaningful game-logic checks; `npm run check` for JavaScript syntax. The tests cover the 27-day / 450-XP reference route, all choice branches, all three endings, financial/intervention evidence, delay arithmetic, invalid inputs, clamped scoring and persistence replay.

## Training limitations and sources

The module is a fictional England-and-Wales training scenario, not a determination of liability or recoverable damages. Before assessed/production learning, have your claims technical/legal lead validate the answers and scoring against current practice. The final note is scored only for topic coverage using explicit terms; **it does not automatically assess reasoning or legal correctness**. The model answer and manager discussion questions support human coaching. A lower bill never directly awards points.

The source conversation is **Gamify Claims Learning** (conversation ID `6abbff62-79cc-83eb-b4c8-1e95268b917c`). Its 60–75 minute concept was narrowed in the subsequent prototype brief to 20–30 minutes and roughly ten decisions. The original illustrative cost attribution is not presented as an actual legal apportionment: the report uses reproducible simulated days and submitted extras instead.

Background consulted 29 September 2026:

- [CPR PD16, paragraph 6.3](https://www.justice.gov.uk/courts/procedure-rules/civil/rules/part16/pd_part16): pleaded hire issues.
- [Lagden v O'Connor [2003] UKHL 64](https://publications.parliament.uk/pa/ld200304/ldjudgmt/jd031204/lagden-1.htm): financial circumstances and credit hire background.
- [Stevens v Equity Syndicate Management [2015] EWCA Civ 93, judgment](https://www.credithirebarrister.com/wp-content/uploads/2017/03/Stevens-v-Equity-Syndicate-Management-Limited-2015-EWCA-Civ-93.pdf): basic hire rate analysis.

No LMS/SCORM/xAPI, manager dashboard or multi-user authentication is implemented. The static Site may be privately hosted, but local progress stays on the learner's device. The optional browser WebMCP tools expose only reading claim progress and opening unlocked evidence; unsupported browsers simply omit them.

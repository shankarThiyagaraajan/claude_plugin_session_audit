# work-audit report template

The layout of the audit report page. **Every value in it is made-up sample data:** "example-shop", its PRs, numbers, files and findings are invented. Keep the CSS, the structure and the chart classes, and replace every piece of content.

## How to assemble the page

The page is made of three parts, in this order:

1. **Head lines:** a `title` element with the text "<project> Session Audit", and a `meta` element with `name="claude:viewport-fit-sides"` and `content="cover"`.
2. **Styles:** the CSS in the "Styles" section below, inside one `style` element, unchanged except for colour tokens you map to the repository's own.
3. **Body:** the HTML in the "Body" section below, with the sample content replaced by the audit's own.

For a page-publishing tool, pass these three parts as they are; the tool adds the document wrapper. For a local file, add the document wrapper that SKILL.md gives, with the head lines and the styles inside its head.

Bars are to scale: width % = value / scale max x 100. State each chart's scale under it.

## Styles

```css
/* Layout: one reading column (max 980px). Summary band first, then sections from cost to actions. Charts are to-scale CSS bars. */
:root {
  --bg: #f3f5f8; --surface: #ffffff; --surface-2: #eceff4; --border: #dbe1ea;
  --ink: #1b2330; --ink-muted: #526079; --ink-faint: #8794a8;
  --accent: #6d4fe0;
  --ok: #0b7a61; --ok-bg: #dcf1eb; --warn: #8a6a0f; --warn-bg: #f4ecd2; --bad: #b8392a; --bad-bg: #f8e1dd; --info: #2f6fd6; --info-bg: #dfe9f9;
  --c-build: #2f6fd6; --c-review: #6d4fe0; --c-fix: #b45309; --c-alt: #0e7490; --c-range: #6d4fe0;
  --font-sans: system-ui, -apple-system, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  --font-mono: ui-monospace, SFMono-Regular, Menlo, Consolas, monospace;
}
@media (prefers-color-scheme: dark) { :root:not([data-theme="light"]) {
  --bg: #0a0e14; --surface: #10151d; --surface-2: #171d27; --border: #263040;
  --ink: #e7edf5; --ink-muted: #96a3b5; --ink-faint: #6b7a8e;
  --accent: #8b7bf0;
  --ok: #2bc8a4; --ok-bg: #12302a; --warn: #e3bf3f; --warn-bg: #363423; --bad: #f06a5e; --bad-bg: #3a1d1b; --info: #5b9bf0; --info-bg: #17263d;
  --c-build: #5b9bf0; --c-review: #8b7bf0; --c-fix: #f0a35e; --c-alt: #4cc3d9; --c-range: #8b7bf0;
  color-scheme: dark; } }
:root[data-theme="dark"] {
  --bg: #0a0e14; --surface: #10151d; --surface-2: #171d27; --border: #263040;
  --ink: #e7edf5; --ink-muted: #96a3b5; --ink-faint: #6b7a8e;
  --accent: #8b7bf0;
  --ok: #2bc8a4; --ok-bg: #12302a; --warn: #e3bf3f; --warn-bg: #363423; --bad: #f06a5e; --bad-bg: #3a1d1b; --info: #5b9bf0; --info-bg: #17263d;
  --c-build: #5b9bf0; --c-review: #8b7bf0; --c-fix: #f0a35e; --c-alt: #4cc3d9; --c-range: #8b7bf0;
  color-scheme: dark; }

* { box-sizing: border-box; }
body { background: var(--bg); color: var(--ink); font-family: var(--font-sans); font-size: 15px; line-height: 1.55; margin: 0; }
.wrap { max-width: 980px; margin: 0 auto; padding-block: 40px 64px; padding-left: max(16px, env(safe-area-inset-left, 0px)); padding-right: max(16px, env(safe-area-inset-right, 0px)); display: grid; gap: 40px; }
h1, h2, h3 { text-wrap: balance; margin: 0; line-height: 1.2; }
h1 { font-size: 30px; font-weight: 700; letter-spacing: -0.01em; }
h2 { font-size: 20px; font-weight: 600; }
h3 { font-size: 15px; font-weight: 600; }
p { margin: 0; max-width: 68ch; }
a { color: var(--accent); }
code { font-family: var(--font-mono); font-size: 0.88em; }
.eyebrow { font-size: 11.5px; letter-spacing: 0.08em; text-transform: uppercase; color: var(--ink-muted); font-weight: 600; }
.muted { color: var(--ink-muted); }
.small { font-size: 13px; }
header { display: grid; gap: 10px; }
header .meta { display: flex; flex-wrap: wrap; gap: 6px 16px; color: var(--ink-muted); font-size: 13.5px; }
section { display: grid; gap: 16px; }
.sec-head { display: grid; gap: 4px; border-top: 1px solid var(--border); padding-top: 20px; }
.sec-head .n { font-family: var(--font-mono); font-size: 12px; color: var(--ink-faint); }
.verdict { background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: 20px; display: grid; gap: 18px; }
.verdict-grid { display: grid; grid-template-columns: repeat(3, minmax(0, 1fr)); gap: 16px; }
.vcell { display: grid; gap: 6px; align-content: start; min-width: 0; }
.vcell .chip { justify-self: start; }
.keynum { display: flex; flex-wrap: wrap; align-items: baseline; gap: 6px 14px; padding-top: 14px; border-top: 1px solid var(--border); }
.keynum .big { font-family: var(--font-mono); font-size: 26px; font-weight: 500; font-variant-numeric: tabular-nums; }
.chip { display: inline-block; font-size: 11.5px; font-weight: 600; letter-spacing: 0.03em; padding: 2px 8px; border-radius: 999px; white-space: nowrap; }
.c-ok { color: var(--ok); background: var(--ok-bg); }
.c-warn { color: var(--warn); background: var(--warn-bg); }
.c-bad { color: var(--bad); background: var(--bad-bg); }
.c-info { color: var(--info); background: var(--info-bg); }
.c-neutral { color: var(--ink-muted); background: var(--surface-2); }
.panel { background: var(--surface); border: 1px solid var(--border); border-radius: 10px; padding: 18px; display: grid; gap: 14px; min-width: 0; }
.two { display: grid; grid-template-columns: minmax(0, 1fr) minmax(0, 1fr); gap: 16px; }
.legend { display: flex; flex-wrap: wrap; gap: 6px 16px; font-size: 12.5px; color: var(--ink-muted); }
.legend span { display: inline-flex; align-items: center; gap: 6px; }
.sw { width: 10px; height: 10px; border-radius: 2px; display: inline-block; }
.bars { display: grid; gap: 7px; }
.bar-row { display: grid; grid-template-columns: 150px minmax(0, 1fr) 72px; gap: 10px; align-items: center; font-size: 13px; }
.bar-row .lab { color: var(--ink-muted); overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
.bar-row .val { font-family: var(--font-mono); font-size: 12px; text-align: right; font-variant-numeric: tabular-nums; }
.track { position: relative; height: 14px; background: var(--surface-2); border-radius: 3px; overflow: hidden; }
.fill { position: absolute; left: 0; top: 0; bottom: 0; border-radius: 3px; }
.group-label { font-size: 12px; font-weight: 600; color: var(--ink-faint); letter-spacing: 0.05em; text-transform: uppercase; margin-top: 6px; }
.stack { display: flex; height: 28px; border-radius: 4px; overflow: hidden; background: var(--surface-2); }
.stack div { display: flex; align-items: center; justify-content: center; color: var(--surface); font-size: 12px; font-weight: 600; font-family: var(--font-mono); min-width: 0; overflow: hidden; white-space: nowrap; }
.axis { display: flex; justify-content: space-between; font-family: var(--font-mono); font-size: 11px; color: var(--ink-faint); padding-left: 160px; padding-right: 82px; }
.range-track { position: relative; height: 18px; background: var(--surface-2); border-radius: 3px; }
.range { position: absolute; top: 3px; bottom: 3px; background: var(--c-range); border-radius: 3px; }
.range.withdrawn { background: transparent; border: 2px dashed var(--ink-faint); top: 2px; bottom: 2px; }
.mark { position: absolute; top: -4px; bottom: -4px; width: 2px; background: var(--ok); }
.consensus { position: absolute; top: 0; bottom: 0; background: color-mix(in srgb, var(--ok) 16%, transparent); border-left: 1px dashed var(--ok); border-right: 1px dashed var(--ok); }
.flow { display: grid; grid-template-columns: repeat(4, minmax(0, 1fr)); gap: 10px; }
.step { background: var(--surface); border: 1px solid var(--border); border-top: 3px solid var(--c-review); border-radius: 8px; padding: 12px; display: grid; gap: 6px; align-content: start; min-width: 0; }
.step.me { border-top-color: var(--ink-faint); }
.step .who { font-family: var(--font-mono); font-size: 11.5px; color: var(--ink-muted); }
.step .out { font-size: 13px; }
.tbl-wrap { overflow-x: auto; border: 1px solid var(--border); border-radius: 10px; background: var(--surface); }
table { border-collapse: collapse; width: 100%; font-size: 13.5px; }
th, td { text-align: left; vertical-align: top; padding: 10px 12px; border-bottom: 1px solid var(--border); }
th { font-size: 11.5px; letter-spacing: 0.06em; text-transform: uppercase; color: var(--ink-muted); font-weight: 600; background: var(--surface-2); white-space: nowrap; }
tr:last-child td { border-bottom: 0; }
td.num { font-family: var(--font-mono); font-variant-numeric: tabular-nums; white-space: nowrap; }
td .ref { display: block; font-family: var(--font-mono); font-size: 11.5px; color: var(--ink-faint); margin-top: 3px; word-break: break-word; }
.sev { width: 1%; white-space: nowrap; }
ol.actions { margin: 0; padding: 0; list-style: none; display: grid; gap: 10px; counter-reset: a; }
ol.actions li { counter-increment: a; display: grid; grid-template-columns: 30px minmax(0, 1fr) auto; gap: 12px; align-items: start; background: var(--surface); border: 1px solid var(--border); border-radius: 8px; padding: 12px 14px; }
ol.actions li::before { content: counter(a); font-family: var(--font-mono); font-weight: 500; color: var(--accent); font-size: 15px; }
ol.actions .what { display: grid; gap: 3px; min-width: 0; }
pre.cmd { margin: 6px 0 0; padding: 8px 10px; background: var(--surface-2); border-radius: 6px; overflow-x: auto; font-family: var(--font-mono); font-size: 12.5px; }
.note { font-size: 13px; color: var(--ink-muted); border-left: 3px solid var(--border); padding-left: 12px; }
footer { font-size: 12.5px; color: var(--ink-muted); border-top: 1px solid var(--border); padding-top: 16px; display: grid; gap: 6px; }
@media (max-width: 760px) {
  .verdict-grid, .two { grid-template-columns: minmax(0, 1fr); }
  .flow { grid-template-columns: minmax(0, 1fr) minmax(0, 1fr); }
  .bar-row { grid-template-columns: 100px minmax(0, 1fr) 62px; gap: 8px; }
  .axis { padding-left: 108px; padding-right: 70px; }
  ol.actions li { grid-template-columns: 24px minmax(0, 1fr); }
  ol.actions li .chip { grid-column: 2; justify-self: start; }
}
@media (max-width: 420px) { .flow { grid-template-columns: minmax(0, 1fr); } h1 { font-size: 25px; } }
```

## Body

```html
<div class="wrap">

<header>
  <div class="eyebrow">Work audit · example-shop · light mode</div>
  <h1>example-shop session audit: efficient or overkill?</h1>
  <p class="muted">The session of 2026-01-15 that shipped PR #41 (saved carts) and PR #42 (cart sync). It covers cost, rule compliance, security and performance.</p>
  <div class="meta"><span>Audit run 2026-01-16</span><span>Sources: work log, briefs file, CLAUDE.md, .claude/rules/, repository at merged head</span></div>
</header>

<div class="verdict">
  <div class="verdict-grid">
    <div class="vcell"><span class="chip c-ok">Process: not overkill</span><p class="small">One review per PR, plus a second only after a blocker. The reviews caught a crash before the merge.</p></div>
    <div class="vcell"><span class="chip c-warn">Inputs: inefficient</span><p class="small">The briefs left out risks that were visible in the code, and the fixes cost 1.4 times the builds.</p></div>
    <div class="vcell"><span class="chip c-bad">Compliance: one breach</span><p class="small">A migration was built by a lower model, against the project's model rule.</p></div>
  </div>
  <div class="keynum"><span class="big">110–170k</span><span class="muted">of 620k agent tokens avoidable (18–27%). The first estimate, 150–250k, was withdrawn after review.</span></div>
</div>

<section>
  <div class="sec-head"><span class="n">1</span><h2>Where the agent tokens went</h2><p class="muted small">From the agent completion figures in the work log. The main session's own tokens are not included.</p></div>
  <div class="panel">
    <h3>By stage</h3>
    <div class="stack" role="img" aria-label="Builds 170k, reviews 210k, fixes 240k">
      <div style="width:27.4%;background:var(--c-build)">170k</div>
      <div style="width:33.9%;background:var(--c-review)">210k</div>
      <div style="width:38.7%;background:var(--c-fix)">240k</div>
    </div>
    <div class="legend"><span><i class="sw" style="background:var(--c-build)"></i>Builds (27%)</span><span><i class="sw" style="background:var(--c-review)"></i>Reviews (34%)</span><span><i class="sw" style="background:var(--c-fix)"></i>Fixes (39%)</span></div>
  </div>
  <div class="two">
    <div class="panel">
      <h3>Each agent run</h3>
      <div class="bars">
        <div class="group-label">PR #41 · 380k</div>
        <div class="bar-row"><span class="lab">Build</span><div class="track"><div class="fill" style="width:75%;background:var(--c-build)"></div></div><span class="val">90,000</span></div>
        <div class="bar-row"><span class="lab">Review 1</span><div class="track"><div class="fill" style="width:50%;background:var(--c-review)"></div></div><span class="val">60,000</span></div>
        <div class="bar-row"><span class="lab">Fix 1</span><div class="track"><div class="fill" style="width:91.7%;background:var(--c-fix)"></div></div><span class="val">110,000</span></div>
        <div class="bar-row"><span class="lab">Review 2</span><div class="track"><div class="fill" style="width:41.7%;background:var(--c-review)"></div></div><span class="val">50,000</span></div>
        <div class="bar-row"><span class="lab">Fix 2</span><div class="track"><div class="fill" style="width:58.3%;background:var(--c-fix)"></div></div><span class="val">70,000</span></div>
        <div class="group-label">PR #42 · 240k</div>
        <div class="bar-row"><span class="lab">Build</span><div class="track"><div class="fill" style="width:66.7%;background:var(--c-build)"></div></div><span class="val">80,000</span></div>
        <div class="bar-row"><span class="lab">Review 1</span><div class="track"><div class="fill" style="width:83.3%;background:var(--c-review)"></div></div><span class="val">100,000</span></div>
        <div class="bar-row"><span class="lab">Fix 1</span><div class="track"><div class="fill" style="width:50%;background:var(--c-fix)"></div></div><span class="val">60,000</span></div>
      </div>
      <p class="muted small">Scale 0 to 120k.</p>
    </div>
    <div class="panel">
      <h3>Findings per PR, by severity</h3>
      <div class="bars">
        <div class="bar-row"><span class="lab">#41 · 8</span><div class="stack" style="height:16px"><div style="width:12.5%;background:var(--bad)"></div><div style="width:25%;background:var(--c-fix)"></div><div style="width:50%;background:var(--c-review)"></div><div style="width:12.5%;background:var(--ink-faint)"></div></div><span class="val">1·2·4·1</span></div>
        <div class="bar-row"><span class="lab">#42 · 6</span><div class="stack" style="height:16px"><div style="width:12.5%;background:var(--c-fix)"></div><div style="width:37.5%;background:var(--c-review)"></div><div style="width:25%;background:var(--ink-faint)"></div></div><span class="val">0·1·3·2</span></div>
      </div>
      <div class="legend"><span><i class="sw" style="background:var(--bad)"></i>Blocker</span><span><i class="sw" style="background:var(--c-fix)"></i>Major</span><span><i class="sw" style="background:var(--c-review)"></i>Minor</span><span><i class="sw" style="background:var(--ink-faint)"></i>Nit</span></div>
      <p class="muted small">Scale 0 to 8 findings.</p>
      <p class="note">The briefs fully explain 2 of the 3 majors.</p>
    </div>
  </div>
</section>

<section>
  <div class="sec-head"><span class="n">2</span><h2>How much was avoidable</h2><p class="muted small">Each bar is the range of avoidable tokens one review gave, on a scale of 0 to 620k. The green band is the agreed range.</p></div>
  <div class="panel">
    <div class="bars">
      <div class="bar-row"><span class="lab">Main audit</span><div class="range-track"><div class="consensus" style="left:17.7%;width:9.7%"></div><div class="range withdrawn" style="left:24.2%;width:16.1%"></div></div><span class="val">150–250k</span></div>
      <div class="bar-row"><span class="lab">Reviewer</span><div class="range-track"><div class="consensus" style="left:17.7%;width:9.7%"></div><div class="range" style="left:16.1%;width:12.9%"></div></div><span class="val">100–180k</span></div>
      <div class="bar-row"><span class="lab">Lead</span><div class="range-track"><div class="consensus" style="left:17.7%;width:9.7%"></div><div class="range" style="left:17.7%;width:9.7%"></div></div><span class="val">110–170k</span></div>
    </div>
    <div class="axis"><span>0</span><span>155k</span><span>310k</span><span>465k</span><span>620k</span></div>
    <div class="legend"><span><i class="sw" style="background:var(--c-range)"></i>Estimate</span><span><i class="sw" style="border:2px dashed var(--ink-faint)"></i>Withdrawn</span><span><i class="sw" style="background:color-mix(in srgb, var(--ok) 30%, transparent)"></i>Agreed band</span></div>
  </div>
  <div class="tbl-wrap"><table>
    <thead><tr><th>Lever</th><th>Status</th><th>Saving</th><th>Why</th></tr></thead>
    <tbody>
      <tr><td>Name the knowable risks in the builder brief</td><td><span class="chip c-ok">Holds</span></td><td class="num">60–100k</td><td>The cart-expiry case was visible in <code>cart/service.py</code> before the build.</td></tr>
      <tr><td>Hand off after the first merge</td><td><span class="chip c-ok">Holds</span></td><td class="num">unknown</td><td>The main session kept growing across both PRs.</td></tr>
      <tr><td>Skip the second review</td><td><span class="chip c-bad">Withdrawn</span></td><td class="num">0</td><td>The second review found the blocker.</td></tr>
    </tbody>
  </table></div>
</section>

<section>
  <div class="sec-head"><span class="n">3</span><h2>How this audit was checked</h2><p class="muted small">Every reviewer could only read: no shell and no edits.</p></div>
  <div class="flow">
    <div class="step me"><span class="who">Main session</span><h3>Audit</h3><p class="out">Findings with IDs.</p></div>
    <div class="step"><span class="who">1 × reviewer</span><h3>Review</h3><p class="out">Contradicted 2 points.</p></div>
    <div class="step"><span class="who">1 × lead</span><h3>Rulings</h3><p class="out">Ruled on 2 disputes.</p></div>
    <div class="step"><span class="who">Security · performance</span><h3>Specialists</h3><p class="out">Ran in parallel, on the merged code.</p></div>
  </div>
  <div class="panel">
    <h3>What the checking cost</h3>
    <div class="bars">
      <div class="bar-row"><span class="lab">Reviewer</span><div class="track"><div class="fill" style="width:14.5%;background:var(--c-review)"></div></div><span class="val">90,000</span></div>
      <div class="bar-row"><span class="lab">Lead</span><div class="track"><div class="fill" style="width:12.9%;background:var(--c-review)"></div></div><span class="val">80,000</span></div>
      <div class="bar-row"><span class="lab">Security</span><div class="track"><div class="fill" style="width:13.7%;background:var(--c-alt)"></div></div><span class="val">85,000</span></div>
      <div class="bar-row"><span class="lab">Performance</span><div class="track"><div class="fill" style="width:15.3%;background:var(--c-alt)"></div></div><span class="val">95,000</span></div>
      <div class="bar-row"><span class="lab"><strong>Audit total</strong></span><div class="track"><div class="fill" style="width:56.5%;background:var(--ink-faint)"></div></div><span class="val"><strong>350,000</strong></span></div>
      <div class="bar-row"><span class="lab">Session audited</span><div class="track"><div class="fill" style="width:100%;background:var(--c-fix)"></div></div><span class="val">620,000</span></div>
    </div>
    <p class="muted small">Scale 0 to 620k.</p>
  </div>
</section>

<section>
  <div class="sec-head"><span class="n">4</span><h2>Rulings on the disputed points</h2></div>
  <div class="tbl-wrap"><table>
    <thead><tr><th>#</th><th>Audit's claim</th><th>Ruling</th><th>Deciding evidence</th></tr></thead>
    <tbody>
      <tr><td class="num">D1</td><td>A second review was not needed</td><td><span class="chip c-bad">Wrong</span></td><td>The second review found the blocker.<span class="ref">CLAUDE.md:42 · work log:88</span></td></tr>
      <tr><td class="num">D2</td><td>The migration should have been built by the stronger model</td><td><span class="chip c-ok">Right</span></td><td>Migrations are a named risk path.<span class="ref">.claude/rules/models.md:7</span></td></tr>
    </tbody>
  </table></div>
</section>

<section>
  <div class="sec-head"><span class="n">5</span><h2>Rule compliance</h2></div>
  <div class="two">
    <div class="tbl-wrap"><table><thead><tr><th colspan="2">Followed</th></tr></thead><tbody>
      <tr><td><span class="chip c-ok">OK</span></td><td>Only the touched tests ran before merge</td></tr>
      <tr><td><span class="chip c-ok">OK</span></td><td>Models named in both PR bodies</td></tr>
    </tbody></table></div>
    <div class="tbl-wrap"><table><thead><tr><th colspan="2">Missed</th></tr></thead><tbody>
      <tr><td><span class="chip c-bad">Breach</span></td><td>The migration in PR #41 was built by a lower model</td></tr>
      <tr><td><span class="chip c-warn">Stale</span></td><td>The progress doc still lists PR #40 as open</td></tr>
    </tbody></table></div>
  </div>
</section>

<section>
  <div class="sec-head"><span class="n">6</span><h2>Security</h2><p class="muted small">No secret value is reproduced here.</p></div>
  <div class="tbl-wrap"><table>
    <thead><tr><th>Severity</th><th>Finding</th><th>Fix</th></tr></thead>
    <tbody>
      <tr><td class="sev"><span class="chip c-warn">Medium</span></td><td>The cart-share route has no ownership check.<span class="ref">api/carts.py:58</span></td><td>Check that the cart belongs to the caller.</td></tr>
      <tr><td class="sev"><span class="chip c-ok">Clean</span></td><td colspan="2">Request bodies are strict, and every new route requires a signed-in user.</td></tr>
    </tbody>
  </table></div>
</section>

<section>
  <div class="sec-head"><span class="n">7</span><h2>Performance</h2><p class="muted small">From code reading. Timings are estimates until measured.</p></div>
  <div class="two">
    <div class="panel">
      <h3><code>GET /carts</code> response time</h3>
      <div class="bars">
        <div class="bar-row"><span class="lab">Budget</span><div class="track"><div class="fill" style="width:50%;background:var(--ok)"></div></div><span class="val">300 ms</span></div>
        <div class="bar-row"><span class="lab">Before</span><div class="track"><div class="fill" style="width:36.7%;background:var(--c-build)"></div></div><span class="val">220 ms</span></div>
        <div class="bar-row"><span class="lab">Now (estimate)</span><div class="range-track"><div class="range" style="left:58.3%;width:25%;background:var(--bad)"></div><div class="mark" style="left:50%"></div></div><span class="val">350–500</span></div>
      </div>
      <div class="axis"><span>0</span><span>150</span><span>300</span><span>450</span><span>600 ms</span></div>
    </div>
    <div class="panel">
      <h3>Requests per action</h3>
      <div class="bars">
        <div class="bar-row"><span class="lab">Page load</span><div class="track"><div class="fill" style="width:50%;background:var(--c-build)"></div></div><span class="val">2</span></div>
        <div class="bar-row"><span class="lab">Save cart</span><div class="track"><div class="fill" style="width:75%;background:var(--c-fix)"></div></div><span class="val">3</span></div>
      </div>
      <p class="muted small">Scale 0 to 4 requests.</p>
    </div>
  </div>
  <div class="tbl-wrap"><table>
    <thead><tr><th>Severity</th><th>Finding</th><th>New or old</th><th>Fix</th></tr></thead>
    <tbody>
      <tr><td class="sev"><span class="chip c-warn">Medium</span></td><td>An N+1 query loads each cart item's price separately.<span class="ref">cart/service.py:120-131</span></td><td>New in #42</td><td>Load the prices in one query.</td></tr>
    </tbody>
  </table></div>
</section>

<section>
  <div class="sec-head"><span class="n">8</span><h2>What to do, in order</h2></div>
  <ol class="actions">
    <li><div class="what"><strong>Add the ownership check to the cart-share route.</strong><span class="muted small"><code>api/carts.py</code>:58</span></div><span class="chip c-warn">Security</span></li>
    <li><div class="what"><strong>Measure <code>GET /carts</code>.</strong><pre class="cmd">make time-route ROUTE=/carts</pre></div><span class="chip c-warn">Performance</span></li>
    <li><div class="what"><strong>Next session:</strong> name the knowable risks in each builder brief.</div><span class="chip c-ok">Process</span></li>
  </ol>
</section>

<footer>
  <span>Unverified: timings until measured.</span>
  <span>The main session's own tokens are not included.</span>
</footer>

</div>
```

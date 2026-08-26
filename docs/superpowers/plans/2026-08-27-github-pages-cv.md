# GitHub Pages CV Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Publish a responsive, terminal-styled single-page CV at `https://georgimkhitaryan.github.io`.

**Architecture:** Two hand-written static files (`index.html`, `style.css`) plus an assets folder, served by GitHub Pages straight from `main`. No build step, no framework, no CI. One ~10-line inline script assembles contact hrefs at runtime; everything else is static markup.

**Tech Stack:** HTML5, CSS3 (custom properties, grid, `@media print`), JetBrains Mono via Google Fonts, `gh` CLI for repo creation and Pages setup.

## Global Constraints

- Repository name: `GeorgiMkhitaryan.github.io` (user site — Pages serves it at the apex path).
- Local working copy: `/Users/georgimkhitaryan/Desktop/docs`, branch `main`.
- Palette, verbatim: `--bg:#0A0C10`, `--surface:#0C0F1A`, `--text:#C9D1D9`, `--muted:#7E89A8`, `--accent:#3BE6FF`.
- Header kicker, verbatim: `// software engineer · 7 yrs · llm apps · realtime backends` — no Python, 7 not 8.
- Python stays in the LLM Engineering, Stack and Skills sections.
- Content comes only from `~/Desktop/legendary-cv/_body.part`. The Armenian bank form in `~/Downloads` is off-limits — it carries family, ID and military-service data.
- Font stack everywhere: `'JetBrains Mono', ui-monospace, SFMono-Regular, Menlo, monospace`.
- No motion beyond the blinking caret.
- `docs/` holds specs and plans only; it is not published content.

---

### Task 1: Scaffold repository and assets

**Files:**
- Create: `.nojekyll`, `README.md`
- Create: `assets/photo.png` (copy of `~/Desktop/legendary-cv/photo-2026.png`, 600×600)
- Create: `assets/Georgi_Mkhitaryan_CV.pdf` (copy of `~/Desktop/legendary-cv/Georgi_Mkhitaryan_CV.pdf`)

**Interfaces:**
- Produces: `assets/photo.png` and `assets/Georgi_Mkhitaryan_CV.pdf`, referenced by `index.html` in Task 3.

- [ ] **Step 1: Copy assets into place**

```bash
cd /Users/georgimkhitaryan/Desktop/docs
mkdir -p assets
cp ~/Desktop/legendary-cv/photo-2026.png assets/photo.png
cp ~/Desktop/legendary-cv/Georgi_Mkhitaryan_CV.pdf assets/Georgi_Mkhitaryan_CV.pdf
touch .nojekyll
```

- [ ] **Step 2: Verify assets landed**

Run: `ls -la assets/ && file assets/photo.png`
Expected: `photo.png` ~270KB PNG 600×600, `Georgi_Mkhitaryan_CV.pdf` ~357KB.

- [ ] **Step 3: Write README.md**

```markdown
# georgimkhitaryan.github.io

Personal CV — https://georgimkhitaryan.github.io

Static `index.html` + `style.css`, no build step. Edit and push; GitHub Pages
serves `main` directly. `.nojekyll` disables the Jekyll pipeline.

- `index.html` — all page content
- `style.css` — terminal theme
- `assets/` — photo and downloadable PDF
```

- [ ] **Step 4: Commit**

```bash
git add -A && git commit -m "Add repo scaffold, photo and downloadable PDF"
```

---

### Task 2: Terminal theme stylesheet

**Files:**
- Create: `style.css`

**Interfaces:**
- Produces: the class names `index.html` binds to in Tasks 3–5 —
  `.term`, `.term-bar`, `.kicker`, `.hero`, `.hero-photo`, `.name`,
  `.contacts`, `.contact`, `.status`, `.btn-download`, `.section`,
  `.prompt`, `.summary`, `.bullets`, `.project`, `.job`, `.job-head`,
  `.job-title`, `.job-dates`, `.stack-grid`, `.stack-row`, `.stack-key`,
  `.two-col`, `.footer`, `.caret`.

- [ ] **Step 1: Write the token block and base layout**

```css
:root{
  --bg:#0A0C10; --surface:#0C0F1A; --text:#C9D1D9;
  --muted:#7E89A8; --accent:#3BE6FF; --line:#1C2230;
  --mono:'JetBrains Mono', ui-monospace, SFMono-Regular, Menlo, monospace;
}
*{box-sizing:border-box;margin:0;padding:0}
body{
  background:var(--bg); color:var(--text); font-family:var(--mono);
  font-size:14px; line-height:1.65; padding:24px 16px;
}
.term{
  max-width:900px; margin:0 auto; background:var(--surface);
  border:1px solid var(--line); border-radius:8px; overflow:hidden;
}
```

- [ ] **Step 2: Add the terminal chrome, caret and prompt headings**

The `.term-bar` is the fake window title bar; `.prompt` renders each section
heading as a shell command in accent colour; `.caret` blinks once per second.

```css
.term-bar{
  display:flex; align-items:center; gap:8px;
  padding:10px 14px; border-bottom:1px solid var(--line);
  color:var(--muted); font-size:12px;
}
.term-bar i{width:11px;height:11px;border-radius:50%;background:var(--line);display:block}
.prompt{color:var(--accent); font-size:13px; margin:32px 0 14px; font-weight:500}
.prompt::before{content:'~/'; opacity:.55}
.caret{
  display:inline-block; width:9px; height:1.05em; background:var(--accent);
  vertical-align:-2px; animation:blink 1s steps(1) infinite;
}
@keyframes blink{50%{opacity:0}}
@media (prefers-reduced-motion:reduce){.caret{animation:none}}
```

- [ ] **Step 3: Add hero, contacts, sections, stack and footer rules**

Written in full during implementation against the class list in
**Interfaces** above. Every class in that list must have a rule.

- [ ] **Step 4: Verify the stylesheet parses**

Run: `python3 -c "print(open('style.css').read().count('{') == open('style.css').read().count('}'))"`
Expected: `True` (balanced braces — a cheap syntax smoke test).

- [ ] **Step 5: Commit**

```bash
git add style.css && git commit -m "Add terminal theme stylesheet"
```

---

### Task 3: Page shell, header and summary

**Files:**
- Create: `index.html`

**Interfaces:**
- Consumes: `style.css` class names from Task 2; `assets/photo.png` and
  `assets/Georgi_Mkhitaryan_CV.pdf` from Task 1.
- Produces: the `<main class="term">` element that Tasks 4–5 append sections to.

- [ ] **Step 1: Write the document head**

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Georgi Mkhitaryan — Software Engineer</title>
<meta name="description" content="Software engineer, 7 yrs — LLM apps, realtime backends, systems integration. Yerevan.">
<meta property="og:title" content="Georgi Mkhitaryan — Software Engineer">
<meta property="og:description" content="LLM apps, realtime backends, systems integration.">
<meta property="og:type" content="profile">
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="style.css">
</head>
```

- [ ] **Step 2: Write the header**

Contains: `.term-bar` reading `georgi@yerevan:~$ cat cv.md`, photo, name
`GEORGI MKHITARYAN` with trailing `<span class="caret"></span>`, the kicker
(exact string from Global Constraints), the contact list, `available ·
on-site / hybrid` status, and the `[ download cv.pdf ]` link pointing at
`assets/Georgi_Mkhitaryan_CV.pdf`.

Contacts are plain text in the markup so they survive with JS disabled:

```html
<ul class="contacts">
  <li class="contact"><span>@</span> <a id="mail" href="#">goshmkhitaryan@gmail.com</a></li>
  <li class="contact"><span>tel</span> <a id="tel" href="#">+374 95 266666</a></li>
  <li class="contact"><span>loc</span> Yerevan · GMT+4</li>
  <li class="contact"><span>in</span> <a href="https://linkedin.com/in/gosh-mkhitaryan" rel="noopener">linkedin.com/in/gosh-mkhitaryan</a></li>
  <li class="contact"><span>gh</span> <a href="https://github.com/GeorgiMkhitaryan" rel="noopener">github.com/GeorgiMkhitaryan</a></li>
  <li class="contact"><span>web</span> <a href="https://ev1000.net" rel="noopener">ev1000.net</a></li>
</ul>
```

- [ ] **Step 3: Write the summary section**

Heading `summary $ head -n 1`, then the paragraph from `_body.part`, with
"Eight years" changed to "Seven years" for consistency with the kicker.

- [ ] **Step 4: Verify it renders**

Open `index.html` in the browser preview and confirm the header, photo,
blinking caret and all six contact rows appear.

- [ ] **Step 5: Commit**

```bash
git add index.html && git commit -m "Add page shell, header and summary"
```

---

### Task 4: LLM Engineering and Projects sections

**Files:**
- Modify: `index.html` (append inside `<main class="term">`)

**Interfaces:**
- Consumes: `.section`, `.prompt`, `.bullets`, `.project` from Task 2.

- [ ] **Step 1: Write the LLM Engineering section**

Heading `llm-engineering $ ls -la`. Five `<li>` items, copied from
`_body.part`: prompt structure and iteration; tool/function calling with
schema validation and deterministic fallback; model output as untrusted
input; cost and latency as budgets; the Vertex AI / Gemini / RAG direction.
Python is retained in this section.

- [ ] **Step 2: Write the Projects section**

Heading `projects $ cat flagship.md`. One `.project` card for ev1000.net —
white-label EV-charging CSMS built solo: landing site, React Native app
(iOS + Android), backend, OCPP 1.6J charger↔cloud comms, payment provider
integration with auth, retries, reconnection and reconciliation. Links to
`https://ev1000.net` with `rel="noopener"`.

- [ ] **Step 3: Verify both sections render and the link resolves**

Reload the preview; confirm five bullets and one project card, and that the
ev1000.net link opens in a new tab.

- [ ] **Step 4: Commit**

```bash
git add index.html && git commit -m "Add LLM engineering and projects sections"
```

---

### Task 5: Experience, Stack, Education and footer

**Files:**
- Modify: `index.html` (append inside `<main class="term">`)

**Interfaces:**
- Consumes: `.job`, `.job-head`, `.job-title`, `.job-dates`, `.stack-grid`,
  `.stack-row`, `.stack-key`, `.two-col`, `.footer` from Task 2.

- [ ] **Step 1: Write the Experience section**

Heading `experience $ git log --oneline`. Five `.job` blocks in this order,
with the bullets from `_body.part`:

| Role | Company | Dates |
|---|---|---|
| Senior Software Engineer | SoftConstruct — iGaming tech, regulated, high-load | 2022 — now |
| Software Engineer | AiMit | 2021 — 2022 |
| Software Engineer | NWSLAB | 2020 — 2021 |
| Three.js Developer | M&D Systems (parallel, contract) | 2020 — 2021 |
| Full Stack Developer | Arakssys | 2018 — 2020 |

- [ ] **Step 2: Write the Stack section**

Heading `stack $ cat package.json`. Six `.stack-row` entries — Lang, AI,
Backend, Integration, Ops, Front — with the values from `_body.part`.
`Lang` keeps `Python · TypeScript · JavaScript`.

- [ ] **Step 3: Write Education, Languages and footer**

Heading `misc $ ls`. Two columns: education (LevelUp '18, TT-Soft '17,
YSC Informatics '15) and languages (Armenian native, Russian fluent,
English professional B2+). Footer: email and `2026`.

- [ ] **Step 4: Verify the full page**

Reload; confirm all eight sections are present in order and no placeholder
text remains.

- [ ] **Step 5: Commit**

```bash
git add index.html && git commit -m "Add experience, stack, education and footer"
```

---

### Task 6: Contact obfuscation script

**Files:**
- Modify: `index.html` (inline `<script>` before `</body>`)

**Interfaces:**
- Consumes: the `#mail` and `#tel` anchors from Task 3.

- [ ] **Step 1: Write the script**

```html
<script>
(function () {
  var u = 'goshmkhitaryan', d = 'gmail.com';
  var cc = '+374', n = '95266666';
  var m = document.getElementById('mail');
  var t = document.getElementById('tel');
  if (m) m.href = 'mai' + 'lto:' + u + '@' + d;
  if (t) t.href = 'tel:' + cc + n;
})();
</script>
```

- [ ] **Step 2: Verify the hrefs are assembled**

In the browser console run `document.getElementById('mail').href` and
`document.getElementById('tel').href`.
Expected: `mailto:goshmkhitaryan@gmail.com` and `tel:+37495266666`.

- [ ] **Step 3: Verify the served HTML has no literal mailto**

Run: `grep -c 'mailto:goshmkhitaryan' index.html`
Expected: `0`.

- [ ] **Step 4: Commit**

```bash
git add index.html && git commit -m "Assemble contact hrefs at runtime"
```

---

### Task 7: Responsive and print rules

**Files:**
- Modify: `style.css`

- [ ] **Step 1: Add the mobile-first breakpoint**

Base styles are single-column. At `min-width:768px` the hero becomes a row
(photo beside the text block) and `.two-col` becomes two grid columns.

```css
@media (min-width:768px){
  .hero{display:flex; gap:28px; align-items:flex-start}
  .two-col{display:grid; grid-template-columns:1fr 1fr; gap:28px}
  body{font-size:15px; padding:40px 24px}
}
```

- [ ] **Step 2: Add print rules**

```css
@media print{
  body{background:#fff; color:#000; padding:0; font-size:10.5pt}
  .term{border:0; background:#fff; max-width:none}
  .term-bar, .btn-download, .caret{display:none}
  .prompt{color:#000; border-bottom:1px solid #999}
  a{color:#000; text-decoration:none}
  .section{break-inside:avoid}
}
```

- [ ] **Step 3: Verify at 375px**

Resize the browser to 375×812, reload, confirm no horizontal scrollbar and
that the photo sits above the text.

- [ ] **Step 4: Verify at 1280px**

Resize to 1280×800, confirm the two-column hero and misc section, and that
the terminal card is centred at max 900px.

- [ ] **Step 5: Commit**

```bash
git add style.css && git commit -m "Add responsive breakpoint and print styles"
```

---

### Task 8: Publish to GitHub Pages

**Files:** none — repository operations only.

- [ ] **Step 1: Create the remote repository**

```bash
gh repo create GeorgiMkhitaryan.github.io --public --source=. --remote=origin --push
```

- [ ] **Step 2: Enable Pages on main**

```bash
gh api -X POST repos/GeorgiMkhitaryan/GeorgiMkhitaryan.github.io/pages \
  -f 'source[branch]=main' -f 'source[path]=/'
```

- [ ] **Step 3: Wait for the first build to succeed**

```bash
gh api repos/GeorgiMkhitaryan/GeorgiMkhitaryan.github.io/pages/builds/latest \
  --jq '.status'
```
Expected: `built`. Retry until it leaves `building`.

- [ ] **Step 4: Verify the live site**

```bash
curl -sSI https://georgimkhitaryan.github.io | head -1
curl -sSI https://georgimkhitaryan.github.io/assets/Georgi_Mkhitaryan_CV.pdf | head -1
```
Expected: `HTTP/2 200` for both.

- [ ] **Step 5: Load the live URL in the browser**

Confirm fonts, photo and layout match the local render, and that the
download button serves the PDF.

---

## Success Criteria

- `https://georgimkhitaryan.github.io` returns 200 over HTTPS.
- Legible with no horizontal scroll at 375px; two-column layout at 1280px.
- The PDF download link returns 200.
- LinkedIn, GitHub and ev1000.net links open the correct targets.
- `Cmd+P` produces a legible black-on-white sheet.
- No personal data beyond what the source CV publishes.

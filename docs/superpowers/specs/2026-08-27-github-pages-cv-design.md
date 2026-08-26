# GitHub Pages CV — Design

**Date:** 2026-08-27
**Owner:** Georgi Mkhitaryan
**Status:** Approved

## Goal

Publish a single-page web CV at `https://georgimkhitaryan.github.io`, targeted at
LLM / agentic-AI and backend engineering roles. The page must read well on a
phone, print cleanly, and offer the existing PDF as a download.

## Content Source

Canonical content: `~/Desktop/legendary-cv/Georgi_Mkhitaryan_CV_Fragment.pdf`
(HTML source: `_body.part` in the same folder).

One deviation from the source, agreed with the owner: the header kicker drops
Python and states 7 years, not 8:

> `// software engineer · 7 yrs · llm apps · realtime backends`

Python stays in the LLM Engineering, Stack and Skills sections, where it
supports the positioning rather than defining it.

Explicitly out of scope: the Armenian bank application form found in
`~/Downloads` (`Georgi Mkhitaryan cv (2).docx`). It contains family members,
passport-style ID and military-service data and must never reach a public page.

## Approach

Plain static `index.html` + `style.css`, served by GitHub Pages directly from
the `main` branch. No build step, no framework, no Actions workflow.

Rejected alternatives:
- **Astro / Vite + Actions** — component model and content/markup separation are
  not worth a build pipeline for one page.
- **Jekyll theme** — themes carry their own visual language; the terminal
  aesthetic would need constant overriding.

## Repository Layout

```
GeorgiMkhitaryan.github.io/
├── index.html
├── style.css
├── assets/
│   ├── photo.png
│   └── Georgi_Mkhitaryan_CV.pdf
├── .nojekyll
├── README.md
└── docs/superpowers/specs/   (design docs, not published content)
```

`.nojekyll` stops GitHub from running the Jekyll pipeline over the files.

## Visual Design

Terminal / developer aesthetic, sharing a palette with the existing PDF so the
site and the file read as one family.

| Token | Value | Use |
|---|---|---|
| `--bg` | `#0A0C10` | page background |
| `--surface` | `#0C0F1A` | terminal window fill |
| `--text` | `#C9D1D9` | body copy |
| `--muted` | `#7E89A8` | dates, labels |
| `--accent` | `#3BE6FF` | prompts, links, caret |

- `JetBrains Mono` throughout, loaded from Google Fonts with a `monospace`
  fallback stack so the page survives a blocked font request.
- Section headings render as shell commands, e.g. `~/experience $ ls -la`.
- A blinking `█` caret in the header; a thin border framing the content as a
  terminal window.
- No scroll animations, parallax or motion beyond the caret.

## Sections

1. **Header** — photo, name, kicker, contacts (email, phone, Yerevan GMT+4,
   LinkedIn, GitHub, ev1000.net), `available` status, `[ download cv.pdf ]`.
2. **Summary** — the 7-years-of-systems paragraph and the move into LLM work.
3. **LLM Engineering** — five bullets from the source CV. Placed above
   Experience because it is the block target roles screen for.
4. **Projects** — ev1000.net as flagship: CSMS, React Native, OCPP 1.6J,
   payment integration, built solo end to end. Linked.
5. **Experience** — SoftConstruct 2022→now, AiMit, NWSLAB, M&D Systems,
   Arakssys, with the bullets from the source CV.
6. **Stack** — Lang / AI / Backend / Integration / Ops / Front table.
7. **Education · Languages** — two compact columns.
8. **Footer** — email and year.

## JavaScript

One inline script, roughly ten lines: it assembles the `mailto:` and `tel:`
hrefs from split string parts at load time, so naive scrapers do not harvest
them from the served HTML.

The owner chose to publish the phone number, having been told it will attract
bot traffic on a public page. Contacts remain visible as plain text when
JavaScript is disabled — only the clickable hrefs depend on the script, so
accessibility is unaffected.

## Responsive & Print

- Single column below 768px, two columns above.
- Verified locally at 375px and 1280px before pushing.
- `@media print` rules produce a legible black-on-white sheet from `Cmd+P`,
  hiding the download button and terminal chrome.

## Success Criteria

- `https://georgimkhitaryan.github.io` serves the CV over HTTPS.
- Legible and correctly laid out at 375px and 1280px.
- The PDF download link resolves.
- Every external link (LinkedIn, GitHub, ev1000.net) opens the right target.
- No personal data beyond what the source CV already publishes.

# shotgrep — ctrl+f for your screenshots

A standalone visual explainer ("blog post visualizer") breaking down
[@xevrion_the1](https://x.com/xevrion_the1)'s
[gyotaku](https://github.com/xevrion/gyotaku) — *"ctrl+f for my screenshots
folder"*: a native Linux Rust app (gpui) that watches your screenshots folder,
OCRs every image with PaddleOCR, indexes the text in SQLite FTS5, and makes it
all searchable in milliseconds. Fully offline, open source.

It also delivers a researched verdict on the question **"is PaddleOCR the best
OCR?"** — yes for this job (PP-OCRv5, best accuracy-per-MB on CPU in 2026),
with the honest asterisk that a Rust app should run PP-OCRv5's *models* via
ONNX Runtime (the RapidOCR playbook), not PaddleOCR's Python runtime.

## What's inside

Single self-contained `index.html` — no build step, no dependencies, works
from `file://`.

- **Verdict-first hero** — what gyotaku is in one line + the PaddleOCR verdict up top
- **The problem** — screenshot hoarding, relatable opener
- **How it works** — 5-step pipeline walkthrough (Watch → Detect → Recognize →
  Index → Search) as a step-through widget in Luke's chosen **Glide** feel:
  big Next button that never moves (probe-measured min-heights), Back,
  step counter, progress bar, keyboard arrows, `touch-action:manipulation`
- **Simulated live demo** — 8 canvas-drawn fake screenshots with known text;
  the search box filters them instantly with match highlighting as you type.
  Clearly labeled simulated; input is 17px (no iOS zoom)
- **2026 OCR showdown** — comparison table (PaddleOCR PP-OCRv5, Tesseract 5,
  EasyOCR, RapidOCR/ONNX, Surya 2, Apple Vision, Google ML Kit: accuracy,
  speed, model size, languages, offline, license) researched via web search
  with inline sources, plus per-constraint picks (smallest binary, mobile,
  GPU box, handwriting)
- **Stack cards** — SQLite FTS5, gpui, fully-offline privacy story
- **Links** — the X post and the GitHub repo

## Constraints honored

- No fake data presented as real: demo + walkthrough labeled SIMULATED;
  comparison figures flagged as directional with sources cited
- No emojis as UI icons (text glyphs like → ✓ ♥ only inside simulated content)
- Zero console errors (verified desktop + 390×844 mobile via Playwright)

## Verify

```bash
cd ~/workspace/screenshot-ctrl-f
python3 - <<'EOF'
from playwright.sync_api import sync_playwright
with sync_playwright() as p:
    b = p.chromium.launch()
    for vp in ({'width':1280,'height':800}, {'width':390,'height':844}):
        pg = b.new_page(viewport=vp)
        errs = []
        pg.on('pageerror', lambda e: errs.append(str(e)))
        pg.goto('file:///home/hatch/workspace/screenshot-ctrl-f/index.html', wait_until='networkidle')
        pg.click('#pipeNext'); pg.click('#pipeNext'); pg.keyboard.press('ArrowRight')
        pg.fill('#demoSearch', 'wifi')
        print(vp['width'], 'errors:', errs if errs else 'none')
        pg.close()
    b.close()
EOF
```

## Not deployed

Built and verified only, per instructions. To ship: push this directory to a
repo and connect it to Cloudflare Pages (Connect-to-Git).

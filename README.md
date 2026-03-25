# dewd cursor

Hand-drawn SVG cursors for your website. Pick a hand, tweak the colors, paste one snippet.

---

## What it is

Five hand-illustrated cursors made from original drawings — not stock icons, not clip art. Each one has two fill layers (black body + white highlights) so they look like actual ink on paper. They react to hover, click, and movement.

**→ Try the picker at [dewd.cool/pages/cursor](https://dewd.cool/pages/cursor)**

---

## Features

- 5 hand-drawn cursors to choose from
- Hover state — tints + tilts on interactive elements
- Click state — hand scales down and rotates
- Smooth follow — floaty lag between mouse and cursor
- Trail — small accent dots that fade behind the cursor
- Everything is configurable from one CSS block + one JS config object
- Works on any site: plain HTML, Webflow, Framer, Squarespace, Shopify, WordPress, React

---

## Quick start

Paste this into your site's `<head>` (or body injection field). That's it.

```html
<style>
  @media (pointer: fine) {
    *, *::before, *::after { cursor: none !important; }
  }

  :root {
    --cursor-fill:       #1a1714;   /* hand color */
    --cursor-fill-white: #ffffff;   /* highlight layer */
    --cursor-hover-fill: #d4522a;   /* color on hover / click */
    --cursor-glow:       #d4522a;   /* drop-shadow on hover */
    --cursor-size:       52px;
    --trail-color:       #d4522a;
  }

  #dewd-cursor {
    position: fixed; top: 0; left: 0;
    width: var(--cursor-size);
    aspect-ratio: 94.18 / 120.6;   /* update to match your chosen cursor's viewBox */
    pointer-events: none; z-index: 99999;
    translate: -4px -4px; will-change: left, top;
  }
  #dewd-cursor svg { width: 100%; height: 100%; transition: transform .15s ease, filter .2s ease; }
  #dewd-cursor svg .cls-black { fill: var(--cursor-fill); transition: fill .15s; }
  #dewd-cursor svg .cls-white  { fill: var(--cursor-fill-white); }
  #dewd-cursor.is-hover svg { transform: scale(1.12) rotate(-6deg); filter: drop-shadow(0 0 6px var(--cursor-glow)); }
  #dewd-cursor.is-hover svg .cls-black { fill: var(--cursor-hover-fill); }
  #dewd-cursor.is-click svg { transform: scale(0.88) rotate(5deg); }
  #dewd-cursor.is-click svg .cls-black { fill: var(--cursor-hover-fill); }

  .dewd-trail {
    position: fixed; width: 5px; height: 5px; border-radius: 50%;
    background: var(--trail-color); pointer-events: none;
    z-index: 99998; opacity: 0; translate: -50% -50%;
  }
</style>

<script>
  document.addEventListener('DOMContentLoaded', function () {

    // ── Config ──────────────────────────────────────
    const CURSOR = {
      smoothing:     0.22,   // 0.1 = snappy, 0.5 = floaty, 1 = instant
      interactables: 'a, button, [role="button"], input, textarea, select, label',
      trail: {
        enabled:     true,
        count:       12,     // dot pool size
        intervalMs:  40,     // min ms between dots
        fadeDuration:'0.5s'
      }
    };
    // ────────────────────────────────────────────────

    // Inject cursor element
    const el = document.createElement('div');
    el.id = 'dewd-cursor';
    el.innerHTML = `PASTE YOUR CHOSEN SVG HERE`;
    document.body.appendChild(el);

    // Trail dots
    const pool = [];
    if (CURSOR.trail.enabled) {
      for (let i = 0; i < CURSOR.trail.count; i++) {
        const d = document.createElement('div');
        d.className = 'dewd-trail'; document.body.appendChild(d); pool.push(d);
      }
    }

    // Smooth follow
    let mx=-200, my=-200, cx=-200, cy=-200, idx=0, last=0;
    const lerp = (a,b,t) => a+(b-a)*t;
    (function tick() {
      cx=lerp(cx,mx,CURSOR.smoothing); cy=lerp(cy,my,CURSOR.smoothing);
      el.style.left=cx+'px'; el.style.top=cy+'px';
      requestAnimationFrame(tick);
    })();

    // Events
    document.addEventListener('mousemove', e => {
      mx=e.clientX; my=e.clientY;
      if (CURSOR.trail.enabled) {
        const now=Date.now();
        if (now-last>=CURSOR.trail.intervalMs) {
          last=now; const d=pool[idx++%CURSOR.trail.count];
          d.style.left=e.clientX+'px'; d.style.top=e.clientY+'px';
          d.style.transition='none'; d.style.opacity='0.55';
          requestAnimationFrame(()=>{
            d.style.transition='opacity '+CURSOR.trail.fadeDuration+' ease';
            d.style.opacity='0';
          });
        }
      }
    });
    document.addEventListener('mouseover', e => { if(e.target.closest(CURSOR.interactables)) el.classList.add('is-hover'); });
    document.addEventListener('mouseout',  e => { if(e.target.closest(CURSOR.interactables)) el.classList.remove('is-hover'); });
    document.addEventListener('mousedown', () => el.classList.add('is-click'));
    document.addEventListener('mouseup',   () => el.classList.remove('is-click'));
  });
</script>
```

---

## Customization

### Colors (CSS variables — change just these)

| Variable | What it does |
|---|---|
| `--cursor-fill` | Default hand color |
| `--cursor-fill-white` | White highlight layer of the SVG |
| `--cursor-hover-fill` | Hand color on hover and click |
| `--cursor-glow` | Drop-shadow color on hover |
| `--cursor-size` | Width of the cursor (height scales automatically) |
| `--trail-color` | Trail dot color |

### Behavior (JS config object)

| Key | Default | What it does |
|---|---|---|
| `smoothing` | `0.22` | Follow lag. `0.1` = snappy, `1` = instant |
| `interactables` | selector string | Elements that trigger hover state |
| `trail.enabled` | `true` | Toggle the dot trail |
| `trail.count` | `12` | Number of dots in pool (more = longer trail) |
| `trail.intervalMs` | `40` | Min ms between spawns (lower = denser) |
| `trail.fadeDuration` | `'0.5s'` | CSS fade-out duration |

### Quick recipes

**Disable the trail**
```js
trail: { enabled: false }
```

**Snap to the mouse with no lag**
```js
smoothing: 1
```

**Remove the glow on hover**
```css
--cursor-glow: transparent;
```

**Freeze the hover tint (no color change)**
```css
--cursor-hover-fill: var(--cursor-fill);
```

**Remove the hover tilt**
```css
#dewd-cursor.is-hover svg { transform: none; }
```

---

## The cursors

| File | ViewBox | Aspect |
|---|---|---|
| `dewd-cursor-1.svg` | `0 0 104.88 100.34` | Wide |
| `dewd-cursor-2.svg` | `0 0 94.18 120.6` | Tall |
| `dewd-cursor-3.svg` | `0 0 61.65 78.95` | Tall |
| `dewd-cursor-4.svg` | `0 0 44.54 65.98` | Tall |
| `dewd-cursor-5.svg` | `0 0 60.45 68.07` | Square-ish |

Each SVG has paths in two classes:

- `.cls-black` — the main hand shape (maps to `--cursor-fill`)
- `.cls-white` — ink highlights (maps to `--cursor-fill-white`)

The picker at [dewd.cool/pages/cursor](https://dewd.cool/pages/cursor) generates the full ready-to-paste snippet for whichever cursor and colors you choose.

---

## How to use SVGs manually

If you want to use the raw files instead of the picker, add the classes to the paths yourself:

- Paths with no `class` attribute → add `class="cls-black"`
- Paths with `class="cls-1"` → change to `class="cls-white"`

---

## Browser support

Works everywhere a pointer device exists. The `@media (pointer: fine)` wrapper hides the system cursor only on mouse/trackpad — touch screens are unaffected.

---

## License

MIT. Use it, modify it, put it in your projects.

---

Made by hand · [dewd.cool](https://dewd.cool)

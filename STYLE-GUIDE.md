# BLOB Studio style guide

How every BLOB studio looks and behaves, so a new one can start from a blank HTML file and still feel like part of the set.

Every studio is its own app in **one self-contained HTML file**. Studios share a look, a layout and a save pattern. They don't share code: each file carries its own copy of the tokens, fonts and theme rules below.

Reference files: `index.html` (the hub), `blob-logo-studio.html` and `blob-record-studio.html`. When this guide and those files disagree, the files win, and this guide should be updated.

---

## 1. Shared vs. your own

| Shared by every studio | Each studio decides |
| --- | --- |
| Colour tokens, fonts, borders, shadows | What the options panel contains |
| Header lockup (wordmark, divider, studio name, edition chip) | Its edition chip text (e.g. `3D`) |
| Workspace layout and phone order | Whether it has an Uploads panel or a Camera / Lights bar |
| Buttons, segments, sliders, fields, swatches, pop-ups | Its own controls and icons |
| The Downloads panel layout and the Presets & project menu | Which export formats it offers (PNG, SVG, ZIP, OBJ, ...) |
| Keyboard shortcuts and slider behaviour | Its project file contents |

---

## 2. Starting a new studio

1. **Make the file.** Work in its own folder (for example `BLOB.Poster/` for sources and notes), and publish the app beside the hub as `blob-<name>-studio.html`, like the other two.
2. **Head.** Copy these from an existing studio:
   - `<meta charset="utf-8">` and `<meta name="viewport" content="width=device-width, initial-scale=1">`
   - The same Content-Security-Policy meta. It blocks every network request, so the studio works offline and nothing leaks out.
   - The same pixel `b` favicon (`<link rel="icon" href="data:image/png;base64,...">`).
   - `<title>BLOB <Name> Studio</title>`
3. **Fonts.** Copy the two `@font-face` lines for **BLOB Pixel** and **BLOB Grid** from the top of `index.html`'s `<style>`. They're embedded as data URIs, so there are no font files to ship. The source TTFs are in `BLOB.Logo/Sources/Blob Studio design/`.
4. **Root element.** Put the whole app inside `<main id="blob-app">` and scope every rule to `#blob-app`.
5. **Tokens.** Paste the token block from section 3.
6. **Build** the header, workspace and Downloads panel with the class names in sections 6 to 8, so the shared look carries over.
7. **Add it to the hub.** Add a card to `index.html` (section 10).

---

## 3. Colour

```css
#blob-app {
  /* Surfaces */
  --paper:#f6f6f1;          /* page background, under the dot grid */
  --panel:#ffffff;          /* panels, fields, secondary buttons */
  --well:#eeefe8;           /* recessed areas: Downloads panel, segment wells */
  --line:#e4e4de;

  /* Text */
  --ink:#232321;            /* body text */
  --quiet:#6b6c67;          /* hints, notes, values */
  --pixel-ink:#000000;      /* every frame, border and hard shadow */

  /* Accents */
  --signal:#b8ff1a;         /* lime: the ONE main download, the LED, the edition chip */
  --on-signal:#000000;
  --dmg-light:#9bbc0f;      /* slider fill, hover on the lime button */
  --dmg-mid:#8bac0f;        /* section bullets, scrollbars, link underlines */
  --dmg-dark:#306230;       /* what's active: pressed segments, toggles, focus rings */
  --on-dmg:#ffffff;
  --splash:#ff5a36;         /* rare highlight, from the logo art */

  /* 90s window chrome */
  --window-face:#c0c0c0;    /* toolbars, tray headers, offline badge */
  --window-title:#4f4f4f;   /* title bars on dialogs and hub cards */
  --window-title-ink:#f7f7f7;
  --bevel-light:#ffffff;
  --bevel-dark:#444444;

  /* Shadows */
  --shadow-ink:4px 4px 0 #000000;        /* windows and panels */
  --shadow-hard-sm:2px 2px 0 #000000;    /* buttons and chips */
  --shadow-hard:4px 4px 0 rgba(0,0,0,.35); /* drop-down lists */

  /* Type */
  --pixel-font:"BLOB Pixel","Press Start 2P",Consolas,monospace;
  --grid-font:"BLOB Grid","BLOB Pixel",Consolas,monospace;

  /* Panel padding */
  --pp:19.55px;             /* 15.3px under 900px wide */
}
```

The hub also uses `--night:#141412` and `--cream:#efeee6` for its dark masthead. Studios don't use them.

**Hover tints:** `#eef3dc` on white buttons and list items, and `#dfe6c8` on quiet buttons and segment hovers.

**Dividers:** `2px dotted #cfd1c7` between sections.

**Page background** (on `html, body`):

```css
background-color:#f6f6f1;
background-image:radial-gradient(#dcded4 1px,transparent 1.2px);
background-size:20px 20px;
```

### How to use the colours

- **Lime (`--signal`) appears once per screen.** It's for the main download button (usually PNG) and its quick icon, plus the small LED and edition chip. Never use it for a toggle.
- **DMG dark green (`--dmg-dark`)** means "this is on": pressed segments, active icon choices, the pressed 3D toggle, selected swatches and focus outlines. **Take photo** is also dark green.
- **Black (`--pixel-ink`)** draws every frame, at 2px.
- Everything else is paper, white or well grey.

---

## 4. Type

| Use | Font | Size | Notes |
| --- | --- | --- | --- |
| Studio name in the header | BLOB Grid (`--grid-font`) | 20px (14px on phones) | lowercase, e.g. `record studio` |
| Buttons, section headings, menu titles, chips, badges, segment labels | BLOB Pixel (`--pixel-font`) | 10px | UPPERCASE, `letter-spacing:.1em`, `line-height:1.2`, `font-weight:400` |
| Body, field labels, notes | `"Segoe UI",Arial,sans-serif` | 14px body, 12 to 13px labels, 10.45 to 12px notes | |
| Slider values | system | 12px | `font-variant-numeric:tabular-nums`, `--quiet` |

Pixel text always turns off smoothing:

```css
font-family:var(--pixel-font);font-size:10px;line-height:1.2;letter-spacing:.1em;
text-transform:uppercase;font-weight:400;-webkit-font-smoothing:none;font-smooth:never;
```

Keep labels short. `PNG`, `SVG`, `ZIP (3)` and `Take photo` all fit on one pixel-font button.

---

## 5. Shape

- **Square corners everywhere:** `border-radius:0`. The only round things are the things that are round in real life, like vinyl labels.
- **Frames:** `2px solid var(--pixel-ink)`.
- **Hard offset shadows, no blur:** `--shadow-ink` on panels and windows, `--shadow-hard-sm` on buttons and chips.
- **Pressed state:** buttons move `translate(2px,2px)` and lose their shadow on `:active`. Turn this off under `prefers-reduced-motion`.
- **Bevels** for window chrome: `inset 2px 2px 0 var(--bevel-light), inset -2px -2px 0 var(--bevel-dark)`.
- **Section bullet:** a 6×6 square in `--dmg-mid` with a 1px ink ring, drawn by `h2::before`. Trays and file menus use a `--signal` bullet instead.
- **Chevrons** are a bordered square rotated 45°: `border-right` and `border-bottom` at 3px in `currentColor`.

---

## 6. Layout

```
┌───────────────────────────────────────────────────────────────┐
│ app-header: [blob] │ <name> studio [3D]     UNDO REDO [3D] OFFLINE │
├──────────────┬─────────────────────────────────┬──────────────┤
│ settings-    │ preview-panel                   │ upload-panel │
│ panel        │  ├ preview-toolbar (grey bar)   │ (optional)   │
│ (Options)    │  ├ stage-fit > logo-stage       │              │
│              │  └ download-panel (well)        │              │
└──────────────┴─────────────────────────────────┴──────────────┘
```

- **`#blob-app`**: `max-width:1440px; margin:0 auto; padding:8px 20px 0`. Over 660px wide it fills the viewport (`height:100dvh`) as a grid of header plus workspace, and the panels scroll inside.
- **`.app-header`**: a flex row with the brand on the left and `.header-right` on the right (history actions, then the 3D toggle, then the offline badge).
- **`.workspace`**: a grid with the options column on the left and the preview in the middle. Add a right column for uploads only when the studio needs one.
  - Left column 290 to 320px, right column 190 to 250px. Use the `--left`/`--right` variables so a collapsed panel can shrink to `48px`.
- **`.preview-panel`**: a grid with rows `auto minmax(0,1fr) fit-content(40%)`: toolbar, stage, then Downloads. The preview is always the biggest thing. Downloads take at most 40% and scroll inside.
- **`.stage-fit > .logo-stage`**: the preview keeps the chosen photo format (`--fmt`, e.g. 1.7778 for 16:9) at the largest size that fits.

**Breakpoints**

| Width | What changes |
| --- | --- |
| ≥ 1500px | Wider side columns |
| ≤ 1100px | Narrower side columns |
| ≤ 900px | `--pp` drops to 15.3px |
| ≤ 660px (phone) | One column. The page is the only scroller. The stage sticks to the top. Order: toolbar, preview, uploads, options, downloads. Header, toolbar and button groups are centred. Big download buttons are replaced by the quick icons. |
| `@container` on the Downloads panel (820 / 720 / 640 / 420px) | The export row and file menu wrap |

---

## 7. Components

Use these class names. The shared theme rules target them.

### Header

```html
<header class="app-header">
  <h1 class="brand" aria-label="BLOB Poster Studio">
    <!-- the black pixel "blob" wordmark SVG, viewBox 0 0 28 9 -->
    <span class="brand-divider"></span><span>poster studio</span><span class="edition-chip">2D</span>
  </h1>
  <div class="header-right">
    <div class="history-actions">
      <button id="undo" class="button quiet" disabled aria-keyshortcuts="Control+Z" title="Undo (Ctrl+Z)">Undo</button>
      <button id="redo" class="button quiet" disabled aria-keyshortcuts="Control+Shift+Z" title="Redo (Ctrl+Shift+Z)">Redo</button>
    </div>
    <span class="offline-badge">OFFLINE</span>
  </div>
</header>
```

- **`.brand-divider`**: a 2px black bar, 24px tall (18px on phones).
- **`.edition-chip`**: lime, 2px ink border, small hard shadow.
- **`.offline-badge`**: a grey bevelled box, which turns DMG green with `.online`.
- **`.view-toggle`** (optional, e.g. the 3D toggle): a white pixel button that turns dark green when `aria-pressed="true"`.

### Panels

- **`.settings-panel`**, **`.upload-panel`**: white, 2px ink border, `--shadow-ink`, `padding:var(--pp)`, thin scrollbar in `--dmg-mid`.
- **`.download-panel`**: the same frame, on `--well`.
- **`.panel-toggle`** is the first child of a collapsible panel. It holds a `.panel-toggle-label` in pixel caps and a `.chev`, sets `aria-expanded` and `aria-controls`, and its id is `toggle-<panel>`. When the panel is collapsed, the app root gets a class like `options-hidden`.
- **`.panel-rail`** (optional): when a side panel collapses, it becomes a 48px rail of 34×34 icon buttons, using the same pressed and hover colours.

### Sections and menus

- **`.control-section`** holds an `h2` (pixel caps with the square bullet), and sections are separated by a dotted top border.
- **`details.control-menu`** / **`details.camera-menu`** are collapsible groups. Their `summary` is pixel caps with a chevron.

### Buttons

| Class | Look | Use |
| --- | --- | --- |
| `.button.primary` | lime, ink border, small shadow, DMG-light on hover | The one main download |
| `.button.secondary` | white, ink border, small shadow, `#eef3dc` on hover | Every other action |
| `.button.quiet` | no frame, `#dfe6c8` on hover | Undo / redo, small helpers |
| `#take-photo` | DMG dark, white text | Capturing a photo of the preview |
| `.text-button` | text with a 2px `--dmg-mid` underline | Inline links |

Use `.button-row` for buttons side by side (flex, 8px gap, equal width).

### Choices

- **`.mode-picker`**: a segmented control. It's a well with a 2px ink frame and 3px padding. Each `button` sets `aria-pressed`, and the pressed one goes DMG dark with an inset `--dmg-mid` highlight. Use `.three` / `.four` for tighter segments.
- **`.icon-choice`**, **`.camera-presets`**: a grid of framed buttons with icons or short labels, coloured the same way.
- **`.swatch`**: 24×24, framed. The selected one gets `outline:2px solid var(--dmg-dark); outline-offset:2px`. `.swatch.checker` shows transparency.

### Fields

- `select`, text inputs and colour buttons all get a 2px ink border with square corners.
- Checkboxes use `accent-color:var(--dmg-dark)`.
- **`.field`**: a column with a 7px gap, a 12px label, and the control below.
- **`.color-row`** + **`.color-swatch-btn`** open the shared `.color-sheet` picker (SV square, hue strip, hex input, optional eyedropper).

### Sliders: the "health bar"

```html
<label class="slider-field"><span>Gloss<output data-value="gloss">35%</output></span>
  <input type="range" min="0" max="100" value="35" data-setting="gloss" aria-label="Gloss"></label>
```

The track is 10px with a 2px ink frame. The filled part is `--dmg-light` with a 1px tick every 6px, and the thumb is a 10×18 black block. The fill reads `--p`, so include the small script from the `blob-studio-sliders` block that keeps `--p` in step with the value. Include the **double-click to reset** script too.

### Pop-ups

- **`dialog.app-dialog`**: a white window with a 2px ink frame and `--shadow-ink`. Its `h3` is a 30px grey title bar (`--window-title`) in pixel caps. Actions sit bottom right in `.dialog-actions`, with Cancel as `secondary` and the confirm button as `primary`.
- **`#notice`**: a polite live region that's hidden by default. With `.error` it shows as a toast at the bottom centre for 7 seconds.

---

## 8. Save and export pattern

Every studio ends with the same **Downloads** panel in the same order. Only the export buttons inside it change from studio to studio.

```html
<section class="download-panel" id="download-panel" aria-label="Image downloads">
  <button type="button" class="panel-toggle" id="toggle-downloads" aria-expanded="true" aria-controls="download-panel">
    <span class="panel-toggle-label">Downloads</span><span class="chev" aria-hidden="true"></span></button>

  <!-- Quick icons: shown when the panel is collapsed, and on phones -->
  <div class="download-rail" role="group" aria-label="Quick downloads">
    <button type="button" class="rail-png" data-download-for="download-png" aria-label="Download PNG"><span>PNG</span></button>
    <button type="button" data-download-for="download-svg" aria-label="Download SVG"><span>SVG</span></button>
  </div>

  <!-- Export row: downloads on the left; take photo, format and size on the right -->
  <div class="export-controls">
    <label class="field png-size"><select id="export-size" aria-label="PNG size">
      <option value="1024">1024 px</option><option value="2048">2048 px</option>
      <option value="4096" selected>4096 px · HD</option></select></label>
    <button id="download-png" class="button primary">PNG</button>
    <button id="download-svg" class="button secondary">SVG</button>
    <!-- studio-specific buttons go here -->
  </div>

  <!-- Presets & project: the same in every studio -->
  <details class="file-menu" id="file-menu"><summary>Presets &amp; project</summary>
    <!-- preset picker (.version-field / .preset-field) -->
    <div class="file-tools">
      <section class="file-group"><h2>Presets</h2> name field + Add preset / Save preset </section>
      <div class="file-group"><h2>Project</h2> name field + Save project / Load project </div>
    </div>
  </details>
</section>
```

**Rules**

- **One lime button**, for the studio's main export. Everything else is `secondary`.
- **Short labels:** the format name (`PNG`, `SVG`, `OBJ`), or `ZIP (n)` with a count. Put the full wording in `title`.
- **Every big export button has a quick icon** in `.download-rail` with `data-download-for="<button id>"`. The rail presses the real button and copies its disabled state and count, so there's no duplicate logic. On phones, the rail replaces the big buttons and the size and format pickers stay.
- **Photo studios** (anything with a camera) add `#take-photo`, a `.png-format` aspect picker (1:1, 4:3, 16:9, 2:1), a `.photos` strip and `ZIP (n)`, with a `.photo-rail` quick icon.
- **Presets & project** works the same everywhere:
  - A **preset** saves only the look (settings) and has a name. Presets are listed in the picker and can be downloaded as `<name>.json`.
  - A **project** saves everything, including uploaded images, as `<name>.json`, shaped like `{ app:"blob-<name>-studio", version:n, settings:{...}, ... }`. The `app` field shows which studio a file belongs to, and `version` lets a later version still read old files.
  - Use `showSaveFilePicker` where the browser has it, and a normal download where it doesn't. Clean file names with a `safeName()` helper.
  - **Autosave** goes to `localStorage` under `blob-<name>-studio-project-v<n>`. Prefix any other keys with `blob-<name>-` so studios on the same site never overwrite each other.

---

## 9. Behaviour

| Input | Does |
| --- | --- |
| Ctrl/⌘ + Z, Ctrl/⌘ + Shift + Z | Undo / redo (header buttons) |
| Arrow keys (not while typing, on sliders, or in pop-ups) | Open and close panels: ← Options, → Uploads, ↑ toolbar panel (Camera / Lights), ↓ Downloads. Map only the panels your studio has. |
| Double-click a slider | Reset it to its starting value |
| Escape | Close the colour sheet or the dialog |

Also:

- Focus is always visible: `outline:2px solid var(--dmg-dark); outline-offset:2px`.
- On touch screens, controls are at least 44px.
- Every icon-only button has an `aria-label` and a `title`.

---

## 10. The hub card

Each studio gets one card in `index.html` inside `.grid`:

```html
<a class="card" href="blob-poster-studio.html">
  <div class="win-title px"><span>Poster</span><span class="win-led"></span></div>
  <div class="card-art poster"><img src="data:image/..." alt=""></div>
  <h2>Poster Studio</h2>
</a>
```

Give the art a background colour from the palette with `.card-art.<name> { background:... }`. Logo uses `--night` and Record uses `--dmg-dark`, so pick something different, such as `--splash`, `--dmg-light` or `--window-face`. The art should be square and embedded as a data URI.

---

## 11. Don'ts

- No rounded corners, soft shadows, gradients on buttons, or extra accent colours.
- No external fonts, scripts, images or requests. The CSP blocks them anyway.
- No second lime button.
- No shared JS or CSS files between studios. Each studio stays a single file.

---

## Appendix: small differences between the two studios today

These are naming leftovers rather than design choices, so a new studio can follow either.

- The theme `<style>` block is `id="blob-studio-theme"` in Record and `id="blob-8b-theme"` in Logo. Prefer `blob-studio-theme`. The slider script is `blob-studio-sliders` / `blob-8b-sliders`.
- The preset picker is `.version-*` in Record and `.preset-*` in Logo.
- The photo strip's open/closed state is stored under `blob-record-photos-open` in Record and `blob-photos-open` in Logo. Use `blob-<name>-photos-open`.

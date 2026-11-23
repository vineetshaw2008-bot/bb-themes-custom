# Design System — Cloudroom

A GUI skin for bb, ported from the Cloudroom fork of bb (`cloudroom/gui`). The
theme lives at `/root/.bb/theme/cloudroom/theme.css` and is activated with:

```bash
bb theme set cloudroom
```

Only the look changes. The CSS does not add, remove, or reorder app data.

---

## 1. Visual Theme & Atmosphere

Cloudroom is a quiet, near-black workspace with a single acid-lime accent. The
chrome (sidebar, panels, menus, cards) stays achromatic and low-contrast; the
lime (`#bfff00`) appears only where the interface wants an action: primary
buttons, checked switches, active tabs, selected rows, focus rings, and the
composer send control.

The result is calm and instrument-like rather than decorative. Surfaces are
separated by hairline borders and a crisp 2px-offset shadow ramp, not by large
value jumps. The one deliberately lifted surface is the chat composer.

**Key characteristics**

- Near-black warm canvas with softened ink, not pure black/white.
- Acid lime (`#bfff00`) as the only brand accent, plus a deep olive (`#4f6b00`)
  equivalent for light mode so lime stays legible on pale grey.
- 8px base radius; cards and dialogs at 10px; the composer at 16px.
- Primary buttons are lime with dark labels and a hairline ring.
- Switches are a lime track with a dark knob.
- Tabs are underline tabs, not filled chips.
- Cards are hairline-bordered with a crisp, short shadow.
- Inter / system sans, monospace for code and terminal.
- Motion is quick (120–150ms) and mostly limited to colour and border.

---

## 2. Colour Tokens

### Dark mode

| Token | Value | Role |
|-------|-------|------|
| `--canvas` | `oklch(0.168 0.004 120)` | Page background |
| `--ink` | `oklch(0.82 0.005 120)` | Foreground text |
| `--card` | ink 4% over canvas | Cards, panels |
| `--popover` | ink 6% over canvas | Menus, popovers |
| `--composer` | ink 11% over canvas | Chat composer |
| `--primary` | `#bfff00` | Accent, primary buttons |
| `--primary-foreground` | `#0b0d00` | Text/icon on lime |
| `--primary-hover` | `#d4ff3d` | Lime hover |
| `--border` | ink 15% over canvas | Default borders |
| `--card-border` | lime 20% over canvas | Card hairline |
| `--card-border-hover` | lime 46% over canvas | Card hover hairline |
| `--muted-foreground` | `oklch(0.78 0 0)` | Secondary text |
| `--subtle-foreground` | `oklch(0.66 0 0)` | Tertiary text |

### Light mode

| Token | Value | Role |
|-------|-------|------|
| `--canvas` | `#e6e6e2` | Page background |
| `--ink` | `#33332e` | Foreground text |
| `--card` | `#eeeeea` | Cards, panels |
| `--popover` | `#f4f4f0` | Menus, popovers |
| `--composer` | `#f2f2ee` | Chat composer |
| `--primary` | `#4f6b00` | Accent (deep olive-lime) |
| `--primary-foreground` | `#ffffff` | Text/icon on olive |
| `--primary-hover` | `#5f8000` | Olive hover |
| `--border` | ink 16% over canvas | Default borders |
| `--card-border` | lime 22% over canvas | Card hairline |

### Semantic colours

Unchanged from bb so status language stays familiar:

- Destructive / error: `oklch(0.56 0.19 22)` dark, `oklch(0.45 0.19 26)` light.
- Warning: `oklch(0.75 0.16 50)` dark, `oklch(0.7 0.16 50)` light.
- Success: `oklch(0.74 0.15 155)` dark, `oklch(0.7 0.15 155)` light.
- Attention: `oklch(0.8 0.15 80)` dark, `oklch(0.74 0.15 80)` light.

---

## 3. Typography

- **Sans**: `"Inter Variable", Inter, "SpotifyMixUI", "CircularSp", "Helvetica Neue", Helvetica, Arial, sans-serif`
- **Mono**: `ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace`
- **Terminal**: JetBrainsMono Nerd Font Mono → MesloLGS NF → Symbols Nerd Font Mono → system mono

Cloudroom keeps bb's compact scale. The theme does not change text sizes; it
only adds font features (`cv02`, `cv03`, `cv04`, `cv11`), antialiasing, and a
lime text selection.

---

## 4. Components

### Buttons

The compiled bb primary button uses `bg-foreground text-background`. The theme
repaints that combination lime:

```css
button[class*="bg-foreground"][class*="text-background"],
a[class*="bg-foreground"][class*="text-background"],
[role="button"][class*="bg-foreground"][class*="text-background"] {
  background-color: var(--primary) !important;
  color: var(--primary-foreground) !important;
  border-color: var(--ring);
  box-shadow: inset 0 0 0 1px ..., 0 2px 0 0 ...;
  font-weight: 600;
}
```

- Lime fill, dark label, hairline inset ring.
- Hover moves to `--primary-hover`; active shifts down 1px.
- `!important` is required because the composer plugin forces descendant
  buttons transparent.

**Send control.** The whole send cluster — the arrow plus the dropdown chevron
— is one lime rounded button with dark icons. Styling the cluster, not the inner
circle, keeps it from overflowing and overlapping nearby controls. The composer
plugin's transparent-button rule lives inside `@layer utilities`, so the
override is declared in the same layer:

```css
@layer utilities {
  [data-promptbox-send-menu] {
    background-color: var(--primary) !important;
    color: var(--primary-foreground) !important;
    border-radius: 0.5rem !important;
    opacity: 1 !important;
  }
  [data-promptbox-send-menu] button {
    background-color: transparent !important;
    color: var(--primary-foreground) !important;
  }
}
```

A layered `!important` beats an unlayered one regardless of specificity, so the
override must join the `utilities` layer to win. When the composer is empty the
green softens slightly; once text is present the full cluster is lime.

### Switches

- Checked: one flat lime pill (border is the same lime, no inset ring), dark knob.
- Unchecked: low-contrast ink track, ink knob.
- Always fully rounded.

```css
[role="switch"][data-state="checked"] {
  background-color: var(--primary);
  border-color: var(--primary);
  box-shadow: 0 1px 2px -1px #0006;
}
[role="switch"][data-state="checked"] > span { background-color: var(--primary-foreground); }
```

Never give the checked track a second, darker outline: the 1px border and any
inset ring must match the lime fill, or the switch reads as a ring-in-a-ring.

### Tabs

Tabs are underlines, not pills:

- Inactive: muted text, transparent background.
- Active: foreground text with `inset 0 -2px 0 0 var(--primary)`.
- No rounded chip background on the active tab.

### Cards, lists, resource rows

- Cards: `--card` fill, `--card-border` hairline, 10px radius, short crisp
  shadow. Hover strengthens the lime hairline.
- Resource panels: same card treatment.
- Resource rows: 8px radius; hover adds a 5% lime wash.
- Automations are turned into separate loop cards: the shared panel becomes
  transparent, rows become bordered cards, and the leading status icon becomes
  a ring (lime/neutral, red for errors, amber for warnings).

### Composer

- One large lifted card: 16px radius, `--composer` fill.
- Shadow: `0 2px 0 0 #00000033, 0 18px 44px -28px #00000099`.
- Focus: lime-tinted border and a faint lime outer ring.
- Reasoning preset (`Low` / `High` / `Max` / `Ultra`) renders as a small
  uppercase lime badge at the end of the composer row.

### Badges and pills

Small `rounded-full text-2xs` badges get a lime hairline, a 9% lime wash, lime
text, 600 weight, uppercase, and `0.06em` tracking. This is what makes the
preset name and status chips read as Cloudroom.

### Inputs

- Text inputs and textareas use `--input` borders.
- Search inputs get a 8px radius and a faint ink wash.
- Checkboxes and radios use `accent-color: var(--primary)`.

### Menus and popovers

- 10px radius, lime-tinted hairline.
- Hovered / highlighted menu items get a 9% lime wash.

### Sidebar

- Active row: neutral ink wash, lime 2px inset bar on the left, foreground text.
- Avoid a heavy lime block; the marker is a thin bar, not a fill.

---

## 5. Elevation

Crisp, short shadows rather than large blur:

| Token | Dark | Use |
|-------|------|-----|
| `--shadow-sm` | `0 2px 0 #0000004d, 0 1px 2px -1px #0006` | Cards, panels |
| `--shadow-md` | `0 2px 0 #0000004d, 0 2px 4px -1px #00000080` | Raised controls |
| `--shadow-lift` | `0 -4px 12px -4px #00000042` | Composer lip |
| `--shadow-lg` | `0 2px 0 #0000004d, 0 4px 6px -1px #00000080` | Menus, dialogs |

Light mode uses the same geometry with ink-tinted shadows (`#33333312` →
`#33333361`).

---

## 6. Code Theme

`pierre-dark.json` and `pierre-light.json` are bundled with the theme and are
registered as `bb:cloudroom:dark` / `bb:cloudroom:light`.

- Dark editor background `#181a17`, light `#e9e9e4`.
- Cursor and selection use Cloudroom lime (`#bfff00` dark, `#4f6b00` light).
- Terminal green is lime; the old Spotify `#1ed760` is gone.
- Strings use a soft lime-green (`#b6e77f` dark, `#476600` light).
- Diff insertion uses lime washes; deletion stays red.

---

## 7. Do's and Don'ts

### Do

- Keep the canvas and chrome achromatic; reserve lime for actions and state.
- Use the lime primary button for one main action per view.
- Use underline tabs.
- Use hairline borders plus the crisp shadow ramp for depth.
- Use the lime switch and lime preset badge consistently.
- Keep motion short (120–150ms) and limited to colour/border.

### Don't

- Don't fill large areas with lime; it is an accent, not a background.
- Don't use pure black or pure white as the canvas.
- Don't use pill-shaped primary buttons; Cloudroom uses 8–10px radii.
- Don't rely on big blurred shadows; use the short offset ramp.
- Don't add extra brand hues beyond lime and the semantic status colours.
- Don't override the composer's transparent-button rule from outside
  `@layer utilities`; it will lose.

---

## 8. Known Limits

- The running bb **Automations** plugin does not render model/preset metadata,
  stat cards, or priority groups. Those exist only in the Cloudroom fork's
  `overview-view.tsx` and cannot be added by CSS alone. The theme styles the
  rows that bb does render.
- Preset/model labels appear at the end of a row only where the app renders
  them (for example, the composer reasoning preset). Plugin, skill, and
  automation cards in this bb build do not include that field.
- Some UI is rendered by plugins with their own stylesheets. Primary buttons
  and switches are covered; plugin-specific layouts may need their own rules.

---

## 9. Selector Reference

Stable hooks used by the theme:

| Hook | What it targets |
|------|-----------------|
| `[data-promptbox]` | Composer form |
| `[data-promptbox-submit-action]` | Send button |
| `[data-promptbox-send-menu]` | Whole send cluster (arrow + chevron) |
| `[data-promptbox-submit-group]` | Send button group |
| `[data-resource-list-panel]` | Resource list panel |
| `[data-resource-row]` | Resource row |
| `[data-resource-collection-viewport]` | Scroll viewport |
| `[data-bb-plugin="automations"]` | Automations plugin subtree |
| `[role="switch"]` | Toggles |
| `[role="tab"]` | Tabs |
| `[data-sidebar]` | Sidebar |
| `[data-surface-recessed]` | Recessed code/terminal surfaces |

Compiled utility combinations used where no data hook exists:

- `button[class*="bg-foreground"][class*="text-background"]` — primary button.
- `[class*="border-border"][class*="bg-card"]` — card.
- `[class*="rounded-full"][class*="text-2xs"]` — small badge/pill.

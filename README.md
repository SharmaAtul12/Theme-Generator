# Palette Studio

Turn a mood into a complete, accessible UI theme. Palette Studio generates a full color system from a starting hue family, previews it live on real components in light and dark, checks the contrast as you work, and exports ready-to-use tokens for CSS, Tailwind v4, or JSON.

Everything runs client-side in the browser. No account, no backend, no API keys.

## Highlights

- **Six curated moods** — Tidal, Orchard, Ember, Orchid, Graphite, and Rose. Each preset carries a hue family, saturation band, and personality the generator reads to shape the rest of the palette.
- **Complete, matched themes** — every generation produces a paired light and dark token set covering backgrounds, surfaces, borders, text, accent states, and status colors (success, warning, danger).
- **Live component preview** — see every token applied to real buttons, forms, cards, and charts. The whole studio chrome wears the theme you are building.
- **WCAG contrast built in** — foreground and background pairs are scored against WCAG 2.1 ratios and labelled AAA, AA, AA Large, or Fail.
- **Fine-grained control** — override the accent with any hex, choose sharp / soft / round corners, and regenerate variations on a seed.
- **Deterministic generation** — a seeded PRNG means the same mood and seed always rebuild the same theme, so a palette you like is never lost to a refresh.
- **Export anywhere** — copy CSS custom properties, a Tailwind v4 `@theme` block, or flat JSON tokens.
- **Save locally** — keep up to 8 generated themes side by side in `localStorage` across reloads.

## Tech stack

- [Next.js 16](https://nextjs.org) (App Router) with Turbopack
- [React 19](https://react.dev)
- [Tailwind CSS v4](https://tailwindcss.com)
- [TypeScript](https://www.typescriptlang.org)
- [Phosphor Icons](https://phosphoricons.com)

## Getting started

Requirements: Node.js 18.18+ (Node 20+ recommended) and npm.

```bash
# install dependencies
npm install

# start the dev server
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) to view the landing page, then head to [http://localhost:3000/studio](http://localhost:3000/studio) to build a theme.

### Scripts

| Script          | Description                                  |
| --------------- | -------------------------------------------- |
| `npm run dev`   | Start the development server                 |
| `npm run build` | Create a production build                    |
| `npm run start` | Serve the production build                   |
| `npm run lint`  | Run ESLint                                    |

## How it works

The generator is rule-based color theory, not a trained model. The output shape is intentionally designed so a model-backed generation API could replace the core function later without touching the UI or the export integration.

1. **Pick a mood.** Each preset in `lib/theme/presets.ts` defines a hue, hue spread, saturation and lightness bands, a default radius, and a representative swatch.
2. **Generate.** `generateTheme()` in `lib/theme/generate.ts` wanders within the mood's bands using a deterministic `mulberry32` PRNG seeded for repeatable results, then derives a full `Theme`. Neutrals borrow a hint of the accent hue so the palette feels unified rather than an accent dropped onto flat grey. Dark mode lifts the accent for presence and avoids pure black for depth.
3. **Preview and tune.** The studio applies the generated tokens as CSS variables to the whole shell. Override the accent, change the radius, flip light/dark, or hit "Surprise me" for a fresh direction.
4. **Check contrast.** `lib/theme/color.ts` computes WCAG 2.1 relative luminance and contrast ratios, grading key text and surface pairs live.
5. **Export.** `lib/theme/export.ts` serializes the theme to CSS custom properties, a Tailwind v4 `@theme` block, or JSON tokens.

### Example CSS export

```css
:root {
  --color-background: #fafbfc;
  --color-surface: #ffffff;
  --color-text: #1a2026;
  --color-accent: #1f85d0;
  --color-accent-hover: #1a6fb0;
  --radius: 12px;
}

[data-theme="dark"] {
  --color-background: #0f141a;
  --color-surface: #171d25;
  --color-text: #e8edf2;
  --color-accent: #4b9fe0;
}
```

## Project structure

```
app/
  page.tsx                 Landing page
  layout.tsx               Root layout, fonts, pre-hydration theme script
  globals.css              Global styles and site theme tokens
  studio/
    page.tsx               Studio route
  components/
    StudioShell.tsx        Studio state, layout, and theme application
    ControlRail.tsx        Mood, accent, and radius controls
    AccentPicker.tsx       Hex accent override
    AccentRamp.tsx         Accent lightness ramp
    PaletteDna.tsx         Palette breakdown
    RadiusControl.tsx      Corner radius selector
    MoodSelector.tsx       Mood chips
    ModeToggle.tsx         Light/dark preview toggle
    SavedThemes.tsx        Saved theme list
    useSavedThemes.ts      localStorage-backed saved themes hook
    landing/               Landing page sections (hero, moods, FAQ, ...)
    panel/                 Export panel, swatch grid, contrast badge, snippets
    preview/               Live preview canvas, form, and chart
    theme/                 Site light/dark provider and toggle
lib/
  theme/
    color.ts               HSL/RGB/hex math and WCAG contrast
    presets.ts             Mood presets and radius scale
    generate.ts            Theme generator (seeded, rule-based)
    export.ts              CSS / Tailwind / JSON serializers
    style.ts               Token-to-CSS-variable helpers
    saved.ts               Saved theme persistence
    index.ts               Barrel export
public/                    Static assets
```

## Token model

Each theme mode (`light` and `dark`) exposes the same token set:

| Token           | Role                              |
| --------------- | --------------------------------- |
| `background`    | Page backdrop                     |
| `surface`       | Card / panel background           |
| `surfaceMuted`  | Subtle fill, hover states         |
| `border`        | Borders and dividers              |
| `text`          | Primary text                      |
| `textMuted`     | Secondary text                    |
| `accent`        | Primary accent                    |
| `accentHover`   | Accent hover state                |
| `accentText`    | Readable text on the accent       |
| `success`       | Positive status                   |
| `warning`       | Caution status                    |
| `danger`        | Error status                      |

A radius value travels with the theme (`sharp` 4px, `soft` 12px, `round` 20px) and is emitted as `--radius`.

## License

Private project. No license granted.

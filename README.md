# Presentation Template

A small template for slide decks written in plain HTML, CSS and JavaScript. You don't need a build step, a framework or an internet connection: open `index.html` in a browser and present.

- Fixed **1280×720 stage**, scaled to fit any window, so the layout you design is the layout the audience sees
- **Step-by-step reveals** with one attribute (`data-step="n"`)
- **Per-slide hooks** for animation and interactivity
- **Sections** with automatic kickers, a footer, a progress bar and a clickable slide counter
- Light and **dark** slides, a built-in **laser pointer**, and fonts bundled locally

## Quick start

```bash
git clone <this-repo> my-talk
cd my-talk
open index.html          # macOS; or just double-click the file
```

To serve it over HTTP (handy for sharing on a local network):

```bash
python3 -m http.server 8000   # then visit http://localhost:8000
```

## Controls

| Key | Action |
| --- | --- |
| `→` `Space` `PageDown` `Enter` | next step, then next slide |
| `←` `PageUp` `Backspace` | previous step / slide |
| `1`–`9` | jump to section *n* |
| `Home` / `End` | first / last slide |
| `F` | toggle fullscreen |
| `C` | toggle the red laser pointer (`Esc` turns it off) |

You can also click the slide counter (bottom right) and type a slide number, or use the `‹ ›` buttons. The URL hash (`#3.1` = slide 3, step 1) keeps track of where you are, so reloading the page stays on the same slide.

## Files

```
index.html     the slides themselves, plus DECK and SECTIONS config
style.css      design tokens (colours, fonts) and layout components
slides.js      per-slide hooks (animations, interactive widgets)
deck.js        the slide engine; you normally don't need to touch it
vendor/fonts/  Inter, Instrument Serif, JetBrains Mono (woff2)
```

## Writing slides

Each slide is a `<section class="slide">` inside `#stage`:

```html
<section class="slide" id="s-example" data-sec="intro">
  <h2>Heading with an <em>accent</em></h2>
  <p class="lead">Always visible.</p>
  <div class="callout" data-step="1">Appears on the first → press.</div>
  <div class="callout blue" data-step="2" data-until="3">Visible on step 2 only.</div>
</section>
```

| Attribute / class | Effect |
| --- | --- |
| `data-step="n"` | element appears from step *n* on |
| `data-until="m"` | …and disappears again from step *m* on |
| `data-steps="n"` | force a slide to have *n* steps (for hooks that need steps without `data-step` elements) |
| `data-sec="id"` | slide belongs to a section in `window.SECTIONS`; it gets a kicker |
| `.rise` | a `data-step` element that also slides up slightly as it appears |
| `.dark` | dark theme for this slide |
| `.nofoot` | hide the footer |
| `.nokicker` | no automatic kicker |

Wrap alternatives in `.swap` to stack them in the same place, so one replaces another without the layout jumping.

### Configuration

At the bottom of `index.html`:

```js
window.DECK = { project: 'Project · Lab', title: 'Shown in the footer on unsectioned slides' };
window.SECTIONS = [
  { id: 'layouts', name: 'Layouts' },
  { id: 'interaction', name: 'Interaction' },
];
```

### Hooks

In `slides.js`, register behaviour under the slide's `id`:

```js
HOOKS['s-example'] = {
  enter(el) {},           // slide becomes current
  leave(el) {},           // slide stops being current
  render(step, el) {},    // every step change
  key(e, step, el) {      // return true to consume the key
    if (e.key === 'ArrowUp') { /* … */ return true; }
    return false;
  },
};
```

See the title slide (an animated canvas started in `enter` and stopped in `leave`) and the interactive slide (bars redrawn in `render`, switched with `↑` `↓` in `key`).

### Components in `style.css`

`.lead` `.body` `.small` `.label` · `.row` / `.grow` (columns) · `.items` (numbered rows) · `.fig` · `.callout` (`.blue` `.green` `.amber`) · `.quote` + `.quote-src` + `.rule` · `.cards` / `.card` · `.bars` · `.pills` · `.src` (source line at the bottom) · colour helpers `.c-blue` `.c-coral` etc.

To retheme the deck, edit the variables on `:root` (and the `#stage.dark` block for dark slides). Changing `--accent` alone changes most of the look.

## Fonts

The bundled fonts are under the [SIL Open Font License 1.1](https://openfontlicense.org): [Inter](https://github.com/rsms/inter), [Instrument Serif](https://github.com/Instrument/instrument-serif) and [JetBrains Mono](https://github.com/JetBrains/JetBrainsMono). They cover Latin text. Chinese and other scripts fall back to fonts installed on the system (PingFang / Noto / Songti), which you can change in the `--sans`, `--serif` and `--cjk` variables.

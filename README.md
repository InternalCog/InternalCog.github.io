# aarongonzales.dev

Personal portfolio site for **Aaron Gonzales**: a three-page static site with a retro-terminal look, built in plain HTML and CSS with no frameworks, no build step and no JavaScript.

**Live:** [aarongonzales.dev](https://aarongonzales.dev) · **GitHub:** [@InternalCog](https://github.com/InternalCog)

---

## Contents

- [Pages](#pages)
- [Project structure](#project-structure)
- [Preview locally](#preview-locally)
- [Deployment](#deployment)
- [How the landing page animation works](#how-the-landing-page-animation-works)
- [Editing the site](#editing-the-site)
- [Design tokens](#design-tokens)
- [Accessibility](#accessibility)
- [Credits](#credits)

---

## Pages

| Page | File | What's on it |
|---|---|---|
| Home | `index.html` | Hero with the typed headline "Automation", the machine artwork, a booting CRT monitor that writes and runs a deploy script, and a spinning cooling fan |
| Experience | `experience.html` | Roles, education, certifications, skills and projects |
| Contact | `contact.html` | Email, LinkedIn and GitHub, plus location and availability |

Every page types out its own header when it loads, and all three share the same navigation (`experience`, `contact`, `github`).

## Project structure

```
.
├── index.html          # Landing page
├── experience.html     # Experience page
├── contact.html        # Contact page
├── assets/
│   ├── machine.webp    # Hero artwork (machine + keyboard), transparent background
│   ├── fan.png         # Fan blades, sharp: shown while the fan spins up
│   ├── fan-blur.png    # Fan blades, motion-blurred: shown at full speed
│   └── favicon.svg     # Browser tab icon
├── .nojekyll           # Tells GitHub Pages to serve files as-is, without Jekyll
└── README.md
```

Each page is self-contained: its CSS sits in a `<style>` block in the page's `<head>`, and layout styles are inline. The only external request is the [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) font from Google Fonts. If it can't load, the pages fall back to the system monospace font.

## Preview locally

No install needed. Either:

- **Open the file:** double-click `index.html`. Every link and image path is relative, so it works straight from disk.
- **Run a local server** (closer to how the live site behaves):

  ```bash
  python3 -m http.server 8000
  # then open http://localhost:8000
  ```

## Deployment

Hosted on **GitHub Pages**, with the domain registered and its DNS managed at **Cloudflare**.

### GitHub Pages

1. Push the files to the default branch (`main`), with `index.html` at the repository root.
2. In the repo, open **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to **Deploy from a branch**, choose `main` and `/ (root)`, and click **Save**.

### Custom domain (Cloudflare DNS)

1. In **Settings → Pages → Custom domain**, enter `aarongonzales.dev` and click **Save**.
2. In Cloudflare, add these two DNS records for the domain, both set to **DNS only** (grey cloud):

   | Type | Name | Content | Purpose |
   |---|---|---|---|
   | CNAME | `@` | `internalcog.github.io` | Root domain, `aarongonzales.dev` |
   | CNAME | `www` | `internalcog.github.io` | `www.aarongonzales.dev` |

   A CNAME on the root domain works here because Cloudflare flattens it into a regular address record behind the scenes.

3. Once GitHub has verified the domain, tick **Enforce HTTPS**. `.dev` domains only load over HTTPS, so the custom domain won't open until GitHub has issued the certificate. The `github.io` address works in the meantime.

### Email

The contact address `hello@aarongonzales.dev` relies on **Cloudflare Email Routing** to forward mail to a personal inbox. It receives and forwards only; replies go out from the personal address.

### Updating the site

Edit the files, then commit and push. GitHub Pages republishes automatically within a minute or two.

## How the landing page animation works

Everything runs on CSS `@keyframes`, with no JavaScript. The live elements sit as layers on top of the machine artwork and are positioned in percentages of the image, so they stay aligned at every screen size.

| Time | What happens |
|---|---|
| 0.3 s | "Automation" types out, one letter per step |
| 1.6 s | Monitor powers on (CRT line flash); fan starts spinning up |
| 2.4 – 4.0 s | Boot log: `GONZO-OS 2.6`, memory check, mount, starting shell |
| 4.2 – 14.9 s | `deploy.py` is typed line by line in vim, then saved with `:wq` |
| 14.3 s | `$ python deploy.py` runs |
| 15.3 – 18.2 s | Script output prints, ending on `deployed in 41s` with a blinking cursor |

**Monitor (`.crt`).** A dark panel laid exactly over the picture's own screen, with a glow, scanlines and a vignette. The text inside is sized in container-query units (`cqw`), so it scales with the image. Each code line is sized to its own text (`width: max-content`, no wrapping) and revealed by growing `max-width` one character per step (`steps(n)`). When a line finishes, the limit is released, so a line always shows in full regardless of how a browser rounds glyph widths at small sizes.

**Fan (`.fan`).** The blades are cut out of the artwork as a round sprite and rotated over the original fan. The fan spins up over 2.5 s to 3 turns per second, and the sharp blades cross-fade into a motion-blurred copy. The blur is what makes it read as fast: at 60 frames per second, sharp blades turning any faster would strobe or appear to run backwards.

**Headline overlap.** The machine overlaps the bottom of "Automation". A second, outline-only copy of the word sits in front of the machine, so the covered letters stay readable across the pipes.

## Editing the site

| To change… | Edit |
|---|---|
| Landing tagline | `index.html`: the line starting `BUILDING REAL SOLUTIONS` |
| Roles, education, certifications, skills, projects | `experience.html`: one `<article>` or list item per entry |
| Contact links, location, availability | `contact.html` |
| Script typed on the monitor | `index.html`: the `.crt-code` spans. Each line's character count must match the `--n` and `steps(n)` values in its `.crt-tN` rule. Spaces inside the script are written as `&nbsp;` so code formatters can't split the lines; keep them that way when editing |
| Animation timing | `index.html`: the delays in the `.crt-*`, `.fan` and `.type-word` rules |
| Accent colour | Search and replace `#e73d1d` across the three pages |

## Design tokens

| Token | Value | Used for |
|---|---|---|
| Background | `#19180e` | Page background; matches the artwork's backdrop |
| Ink | `#dcdbd1` | Main text; taken from the machine's casing |
| Dim | `#a9a89c` | Secondary text, labels, dates |
| Accent | `#e73d1d` | Cursors, prompts, the active-page underline; taken from the artwork's labels |
| Rule | `#34332a` | Section dividers |
| Typeface | JetBrains Mono 400 / 500 / 800 | All text |

## Accessibility

- **Reduced motion:** visitors with *reduce motion* turned on in their operating system see every page in its finished state, with no typing, boot sequence or spinning fan.
- **Screen readers:** decorative layers (the monitor overlay, fan, outline headline) are hidden from them, and the artwork has descriptive alt text.
- **Contrast and targets:** text meets WCAG AA contrast on the dark background, and links and buttons are at least 44 px tall.
- **Small screens:** layouts reflow down to phone width without horizontal scrolling.

## Credits

- **Design and build:** Aaron Gonzales, with Claude (Anthropic).
- **Hero artwork:** base image generated with Midjourney; keyboard drawn by Claude Design, live monitor and fan animation added on top.
- **Typeface:** [JetBrains Mono](https://www.jetbrains.com/lp/mono/), SIL Open Font License 1.1.

---

<sub>Views expressed on this site are my own and do not represent the U.S. Air Force or the U.S. Government.</sub>

# GRAVITY — Digital Studio

A scroll-driven studio landing page with a live 3D scene. As you scroll, a cluster of spheres drops under simulated gravity, bounces, reassembles into a heart, then rushes past the camera. A drawer lets visitors switch the sphere palette.

Built by AI from a prompt found online. Copy it, open it, or paste it into your own AI and make it yours, with no need to build it from scratch.

---
## Website details
| Field | Details |
|---|---|
| Website name | Diet Soda |
| Website link | https://gravity-prompt.netlify.app |
| GitHub Repository | https://github.com/Elliott7710/gravity |
---


## Features

- Three.js scene with simulated physics: mass, collisions, bounce, floor, and mouse repulsion
- Four scroll stages: hero cluster, free-fall, heart shape, then fly-through
- Background color shifts from blue to lime to pink as you scroll
- Palette drawer with four themes: Royal Blue & Pearl, Rose Blush & Crème, Volt Lime & Slate, Arctic Sky & Crystal
- Loading screen with a progress counter
- Smooth custom wheel scrolling
- Glass-style header, stat cards, and a footer with a scrolling marquee
- Fewer spheres on phones (54 instead of 96) to keep it smooth

## Tech

HTML, CSS and JavaScript with [Three.js](https://threejs.org) `0.160.0` loaded from unpkg. No build step and no dependencies to install.

## Setup

**Option 1: just open it.** Double-click `index.html`. You need an internet connection.

**Option 2: run a local server** (needs [Node.js](https://nodejs.org)):

```bash
npm start
```

Then open the address it prints, usually http://localhost:3000. Use this if the page stays blank when opened as a file.

**Put it online for free:** push this folder to GitHub, then go to Settings → Pages and deploy from the `main` branch.

## Make it yours

Open `index.html` and change:

| What | Where |
|---|---|
| Studio name | the `wordmark` links and the `<title>` |
| Headline and section text | the `<section>` blocks inside `<main>` |
| Stat cards | the three `stat-card` articles |
| Email and footer links | the `<footer id="footer">` block |
| Sphere palettes | the `palettes` array at the top of the `<script type="module">` |
| Number of spheres | `ballCount` in `createScene` |
| Physics feel | the `params` object (damping, bounce, mouse push) |
| Scroll timing | the `smoothstep(...)` values that set each stage |

Quick way: paste `index.html` into your AI and say what you want, for example *"Rename this to ORBIT, use a purple palette, and write copy for a music studio."*

## Good to know

- **No hosted images or video.** Everything is drawn in code. The only things loaded from the internet are Three.js and the fonts (Space Grotesk, JetBrains Mono, Inter, Raleway).
- **Needs a decent GPU.** The glass-like spheres are heavy to render. If it feels slow, lower `ballCount`.
- **Custom scrolling.** The page takes over the mouse wheel to scroll smoothly.
- **Demo content.** The studio name, email (`hello@gravity.studio`), stats and links are placeholders.

## Credits

Original prompt: _add the source link here_

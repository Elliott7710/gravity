# Recreate this site as a single HTML file: Gravity — Digital Studio

You are an expert creative front-end developer. Produce a **single self-contained `index.html`**
that reproduces the project below **exactly** — same layout, sections, visuals, motion, and
interaction. Pure HTML/CSS/JS in one file: no build step, no framework, no bundler. Use ES modules
with a CDN importmap for Three.js. Hardcode every value given here as a fixed constant. Rebuild each
component described below as a section of the one file.

## What it is

A premium one-page site for a fictional independent digital studio called **GRAVITY**, built around
a full-screen real-time WebGL "gravity field" of hyper-real pastel spheres rendered with Three.js.
On load, a minimal pre-roll loader calibrates while the 3D scene warms up; when it finishes, ~96
glossy spheres (glass + matte physical materials) sweep in from offscreen and converge into a
floating elliptical cluster behind the hero. As you scroll through four full-height sections, the
field is driven by scroll progress: it (1) floats in the hero, (2) surrenders to gravity and bounces
off the floor, (3) reassembles into a solid **heart** silhouette, and (4) lifts off and rushes past
the camera into the lens, fading to nothing. The whole palette morphs along the scroll — hero cobalt
blue → volt-lime → rose-pink — and the background radial gradient eases between matching tints. The
cursor repels the spheres everywhere, with a momentum boost so a fast flick throws them hard. Scroll
is smoothed (Lenis-style). The look: clean white-slate studio, glassmorphic chrome, Raleway display
type, mono labels.

## Page shell and libraries

- Use an importmap loading **Three.js r0.160+** as an ES module from a CDN, for example:
  `https://unpkg.com/three@0.160.0/build/three.module.js`.
- No other JavaScript libraries are required. Reimplement smooth scroll and entrance/scroll reveals
  in vanilla JavaScript.
- Lucide icons are drawn as inline SVG.
- Fonts via Google Fonts:
  `https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;700;900&family=JetBrains+Mono:wght@400;500;700&family=Inter:wght@300;400;500;600;700&family=Raleway:wght@400;500;600;700;900&display=swap`
- CSS variables:
  - `--font-sans: "Inter"`
  - `--font-display: "Space Grotesk"`
  - `--font-mono: "JetBrains Mono"`
  - `--font-raleway: "Raleway"`
- Global CSS reset:
  - `html, body { margin:0; padding:0; width:100%; }`
  - `body { overflow-x:hidden; }`
  - `-webkit-font-smoothing: antialiased`
  - Base text color `#1a1a1a`
  - Default body background `#f7fafc`
- Custom scrollbar:
  - Width `8px`
  - Transparent track
  - Thumb `rgba(14,42,197,0.18)` with radius `99px`
  - Hover thumb `rgba(14,42,197,0.32)`
- Page structure:
  - A fixed gradient div at `z-index:-10`
  - A fixed full-screen WebGL canvas at `z-index:0`
  - The loader at `z-index:100`
  - The header at `z-index:30`
  - Four `min-h-100svh` sections at `z-index:10`
  - A footer after the sections

## Smooth scroll

Implement vanilla smooth wheel scrolling:

```js
// intercept wheel
// accumulate a target scroll
// each rAF lerp window.scrollY toward target with lerp factor 0.09
// wheelMultiplier = 1
// keep native scrollbar behavior
// clamp target to [0, maxScroll]
```

The feel should be a slow, weighty glide.

## Scroll and pointer tracking

On scroll and resize, calculate with rAF throttling:

```js
const vh = window.innerHeight || 1;
const progress = window.scrollY / vh;
control.progress = progress;
```

Calculate the background stage:

```js
const stage = progress > 1.55 ? 2 : progress > 0.7 ? 1 : 0;
// 0 hero, 1 lime, 2 pink
```

Track the normalized cursor:

```js
const mousePos = { x:99, y:99, isDown:false };

window.addEventListener('pointermove', e => {
  mousePos.x = (e.clientX / window.innerWidth) * 2 - 1;
  mousePos.y = -(e.clientY / window.innerHeight) * 2 + 1;
});

window.addEventListener('mousedown', () => mousePos.isDown = true);
window.addEventListener('mouseup', () => mousePos.isDown = false);
```

## Background gradients

A fixed `inset:0` gradient div at `z-index:-10`:

- `transition: background 1.2s cubic-bezier(0.65,0,0.35,1)`
- Hero/Royal Blue:
  `radial-gradient(circle at center, #ffffff 0%, #ecefff 35%, #c2d1ff 100%)`
- Lime:
  `radial-gradient(circle at center, #ffffff 0%, #fbffe5 38%, #e8ff9c 100%)`
- Pink:
  `radial-gradient(circle at center, #ffffff 0%, #fff0f1 38%, #ffd1d5 100%)`

The other hero palettes exist in the Studio Controls drawer, but Royal Blue is the default.

## Layout and sections

Sections are `pointer-events:none` containers with `pointer-events:auto` re-enabled on their text blocks,
so the canvas behind receives cursor input in empty areas.

Sections 02–04 and the footer use a Reveal entrance:

- Initial: `opacity:0; transform:translateY(42px); filter:blur(8px)`
- Final: `opacity:1; transform:translateY(0); filter:blur(0)`
- Duration: `1.5s`
- Easing: `cubic-bezier(0.16,1,0.3,1)`
- IntersectionObserver trigger: approximately 45% visible, once
- Optional stagger delay per element

## Header

Fixed, full-width header:

- Height `80px` mobile / `96px` desktop
- Padding `24px` mobile / `48px` desktop
- Flex space-between
- `pointer-events:none`; interactive children re-enable pointer events
- Entrance: opacity `0`, y `-16` to opacity `1`, y `0` over `0.8s`
- Easing: `cubic-bezier(0.16,1,0.3,1)`

### Left

- Wordmark: `GRAVITY`
- Font: Space Grotesk, 900
- Size: `18px` mobile / `20px` desktop
- Letter spacing: `0.25em`
- Color: `#0a0a0a`

### Center

Visible at desktop only:

- Links: `Works · Services · Team · Story · Contact`
- Raleway 14px medium
- Gap `48px`
- Hover opacity `0.6`

### Right

Two controls with gap `10px` mobile / `12px` desktop.

1. Round sliders icon button:
   - Size `40px` mobile / `44px` desktop
   - Rounded full
   - Glass: border `rgba(255,255,255,0.4)`, background `rgba(255,255,255,0.20)`,
     backdrop blur `20px`
   - Inset highlight:
     `inset 0 1.5px 2px rgba(255,255,255,0.7), 0 8px 30px rgba(0,0,0,0.04)`
   - Hover background `rgba(255,255,255,0.30)`
   - Inline Lucide sliders-horizontal icon, `16px`

2. Contact pill:
   - Height `44px`
   - Left padding `20px` mobile / `24px` desktop
   - Right padding `6px`, vertical padding `6px`
   - Rounded full
   - Glass background `rgba(255,255,255,0.12)`, backdrop blur `20px`
   - Split white border
   - Label: `Contact us`
   - Raleway 13px mobile / 14px desktop, medium
   - Blue orb `32px`, matching gradient:
     `radial-gradient(circle at 50% 30%, #5175ff 0%, #0e2ac5 55%, #020b40 100%)`
   - White chevron-right icon
   - Orb scales to `1.05` on hover

## Section 01 — HERO

Minimum height `100svh`, flex align-end.

- Padding: `48px 24px` mobile; `56px 48px` desktop
- Bottom-aligned row
- On mobile: column, gap `32px`
- Desktop: row, items-end, justify-between

### Left column

- Eyebrow:
  - `[ Digital studio ]`
  - Raleway medium
  - `12px` mobile / `14px` desktop
  - Letter spacing `0.05em`
  - Uppercase
  - Color `#0E2AC5`
  - Bottom margin `24px`
  - Entrance y `14`, delay `0.15s`
- Headline:
  - `Less noise. More gravity.`
  - Raleway medium
  - `2.6rem` mobile
  - `96px` desktop
  - Leading `0.95`
  - Tracking `-0.055em`
  - Color neutral-950
  - Desktop `white-space:nowrap`
  - Entrance y `22`, delay `0.28s`, duration `1s`

### Right column

- Desktop width `260px`
- Entrance delay `0.45s`
- Paragraph:
  - `We design digital experiences that remove distraction and amplify presence.`
  - Raleway 15px medium
  - Leading `1.35`
- Credit:
  - `© 2026 — Independent digital agency`
  - JetBrains Mono 10px
  - Letter spacing `0.18em`
  - Uppercase
  - Neutral-500
  - Margin-top `20px`
- Desktop scroll cue:
  - Arrow-down icon `14px`
  - JetBrains Mono 10px
  - Letter spacing `0.2em`
  - Text `Scroll`
  - Margin-top `16px`
  - Pulse animation

The hero content fades and rises after the loader completes.

## Section 02 — PHYSICS

Minimum height `100svh`, flex align-center.

- Padding: `128px 24px 160px` mobile
- Desktop: `160px 48px`
- Desktop 12-column grid
- One column on mobile
- Gap `40px` mobile / `64px` desktop

### Left column

- Desktop spans `7` columns
- Label:
  - `[ 02 — Physics ]`
  - JetBrains Mono 11px
  - Letter spacing `0.25em`
  - Uppercase
  - `#0E2AC5` at 80% opacity
- Heading:
  - `When structure`
  - `lets go.`
  - Raleway medium
  - `2.6rem` mobile
  - `80px` desktop
  - Leading `1.0` mobile / `0.92` desktop
  - Tracking tight
  - Margin-top `24px`
- Paragraph:
  - Raleway 16px mobile / 19px desktop
  - Leading `1.55`
  - Neutral-700
  - Max-width `32rem`
  - Margin-top `32px`
  - Text:
    `Keep scrolling and the field surrenders to gravity. Every sphere obeys real simulated physics — mass, momentum, restitution — tumbling down and bouncing off the floor until the energy finally settles.`

### Right column

- Desktop spans `5` columns
- Desktop padding-top `12px`
- Three glass stat cards with gap `14px`

Each card:

- Outer rounded `22px`
- One-pixel gradient border ring
- Drop shadow `0 14px 40px -12px rgba(14,42,197,0.18)`
- Lifts `-2px` on hover
- Inner rounded `21px`
- Background `rgba(255,255,255,0.25)`
- Backdrop blur `20px`
- Padding `20px 24px`
- Top hairline sheen
- Header in JetBrains Mono 10px uppercase, neutral-400
- Small blue dot with glow
- Value in Raleway 18px semibold
- Description in Raleway 13px neutral-500

Cards:

1. `01 · Solver`, `Verlet`, dot, `4 sub-steps`, `Stable stacking at 60fps`
2. `02 · Restitution`, `0.65 — bouncy`, dot, `Energy lost`, `Lost on every contact`
3. `03 · Bodies`, `Real-time GPU`, dot, `No pre-baked animation`, `Purely live simulation`

## Section 03 — FORM

Minimum height `100svh`, flex column, justify-between.

- Padding: `144px 24px` mobile / `176px 48px` desktop

Top:

- Label: `[ 03 — Form ]`
- JetBrains Mono 11px
- Letter spacing `0.25em`
- `#0E2AC5` at 80% opacity
- Heading:
  - `Chaos, then form.`
  - Raleway medium
  - Same scale as the Physics heading
  - Margin-top `24px`

Bottom, right-aligned on desktop:

- Max-width `384px`
- Paragraph:
  - Raleway 16px mobile / 19px desktop
  - Leading `1.55`
  - Neutral-700
  - Text:
    `Out of free-fall, the field reassembles — every bubble finding its place in a single shape. Move your cursor through it and watch the order ripple, scatter, and recover.`
- Pill:
  - Margin-top `28px`
  - Inline flex
  - Gap `8px`
  - Padding `10px 16px`
  - Rounded full
  - Border `rgba(255,255,255,0.55)`
  - Background `rgba(255,255,255,0.35)`
  - Backdrop blur `12px`
  - Text `Swipe through it`
  - JetBrains Mono 10px uppercase
  - Letter spacing `0.2em`
  - Inline lucide mouse-pointer icon `14px`

## Section 04 — RELEASE

Minimum height `100svh`, centered column.

- Padding: `144px 24px` mobile / `176px 48px` desktop
- Label: `[ 04 — Release ]`
- JetBrains Mono 11px
- `#0E2AC5` at 80% opacity
- Heading:
  - `And then,`
  - `weightless.`
  - Raleway medium
  - `2.8rem` mobile
  - `88px` desktop
  - Leading `1.0` mobile / `0.92` desktop
  - Margin-top `28px`
- Paragraph:
  - Raleway 16px mobile / 19px desktop
  - Leading `1.55`
  - Neutral-700
  - Max-width `448px`
  - Margin `32px auto 0`
  - Text:
    `The whole field lifts off the screen and rushes past you — every sphere accelerating into the lens until nothing remains but light. Less noise.`

## Footer

- Background `#0a0a0a`
- White text
- Rounded top corners `40px` mobile / `64px` desktop
- `overflow:hidden`
- Two soft radial blurred glow blooms

### Marquee

- Border-bottom `rgba(255,255,255,0.10)`
- Padding `20px 0`
- Overflow hidden
- Track translates from `0` to `-50%` over `26s linear infinite`
- Font size `clamp(28px, 6vw, 64px)`
- Raleway medium
- Color `rgba(255,255,255,0.90)`
- Content repeated twice for seamless looping
- Word padding `0 32px`
- Blue dots `10px`, background `#2f69ff`
- Sequence:
  - `Less noise`
  - `More gravity`
  - `Design with weight`
  - `Let's build`

### Footer body

- Padding `64px 24px 40px` mobile / `96px 48px 40px` desktop
- Desktop 12-column grid
- Gap `48px`

CTA:

- Desktop spans `5` columns
- Mono 11px uppercase, letter spacing `0.25em`, white at 40%
- Text: `[ Let's collaborate ]`
- Heading:
  - `Have an idea`
  - `with real weight?`
  - Raleway medium
  - `36px` mobile / `60px` desktop
  - Leading `0.95`
- Email pill:
  - `mailto:hello@gravity.studio`
  - Padding `8px 8px 8px 28px`
  - Rounded full
  - Border `rgba(255,255,255,0.15)`
  - Background `rgba(255,255,255,0.05)`
  - Hover background `rgba(255,255,255,0.10)`
  - Raleway 16px medium
  - Blue orb `40px`
  - Lucide arrow-up-right icon
  - Icon rotates `45deg` on hover

Link columns:

- Desktop spans `7` columns
- Three columns on desktop
- Grid of `2` columns mobile / `3` columns small screen and desktop
- Gap `32px`

Column titles:

- Mono 10px uppercase
- Letter spacing `0.25em`
- White at 40%

Lists:

- Studio: `Works`, `Services`, `Process`, `Careers`
- Company: `Story`, `Team`, `Journal`, `Contact`
- Social: `Twitter`, `Instagram`, `LinkedIn`, `Dribbble`
- Raleway 15px
- White at 75%
- Hover white
- Small arrow-up-right icon slides in on hover

Bottom bar:

- Margin-top `80px` mobile / `112px` desktop
- Padding-top `28px`
- Border-top `rgba(255,255,255,0.10)`
- Flex space-between
- Left:
  - `GRAVITY` wordmark
  - Blue dot
  - Copyright `© 2026 Digital Studio — All rights reserved.`
- Right:
  - Three round social buttons
  - Size `40px`
  - Border `rgba(255,255,255,0.15)`
  - Twitter, Instagram, and LinkedIn inline SVG icons

## Studio Controls drawer

Opened by the header sliders button.

- Full-height right drawer
- Width `100%` mobile / `420px` desktop
- Background `rgba(255,255,255,0.95)`
- Backdrop blur `20px`
- Border-left `#f5f5f5`
- Padding `32px`
- Z-index `50`
- Initial x `100%`
- Spring-like entrance and exit
- Scrim:
  - Full viewport
  - Z-index `40`
  - `rgba(10,10,10,0.40)`
  - Backdrop blur `4px`
  - Closes when clicked

Drawer header:

- Sliders icon and close X button
- Title `Studio Controls`
- Space Grotesk black
- 14px
- Letter spacing `0.2em`
- Uppercase

Below the header:

- Mono 12px italic neutral-400 note:
  `Set the hero sphere palette. As you scroll, the scene morphs to volt-lime, then rose-pink as the heart assembles.`
- Section title:
  - Mono 10px
  - Letter spacing `0.2em`
  - Bold uppercase
  - Sparkles icon
  - `Hero Palette Shaders`

Four selectable theme cards:

| id | name | description | primaryColor | ballColor |
|---|---|---|---|---|
| roseGold | Royal Blue & Pearl | Hyper-distinct cobalt spheres floating in soft white-slate gradients. | `#2F69FF` | `#2F69FF` |
| pinkBlush | Rose Blush & Crème | Sweet candy pinks and juicy strawberry accents in a pearl studio. | `#FFA6B3` | `#FFC5C2` |
| voltLime | Volt Lime & Slate | Electric neon volt-lime against a clean white-slate horizon. | `#E1FC03` | `#E1FC03` |
| arcticBlue | Arctic Sky & Crystal | Cool arctic crystal blues with a translucent glass sheen. | `#96E5FF` | `#96E5FF` |

Selected card:

- Background neutral-950
- White text
- Scale `1.02`
- Includes swatch using `primaryColor`

Selecting a palette rebuilds the complete Three.js scene and sets the sphere `ballColor`.

Drawer footer:

- `RENDER ENGINE / THREE.JS WEBGL`
- `DYNAMICS SOLVER / 3D COLLISION VERLET`
- Mono 9px neutral-400
- Reset Defaults button with rotate-ccw icon
- Reset restores Royal Blue and `#2F69FF`

## Three.js renderer and scene

Fixed full-screen canvas:

- Pointer events none
- Z-index `0`
- Inside a fixed `inset:0` container

Renderer/camera:

```js
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(
  38,
  container.clientWidth / container.clientHeight,
  0.1,
  100
);
camera.position.set(0, 0, 11);

const renderer = new THREE.WebGLRenderer({
  canvas,
  antialias: true,
  alpha: true,
  powerPreference: 'high-performance'
});

renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
renderer.setSize(container.clientWidth, container.clientHeight);
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.15;
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
```

Lights:

- HemisphereLight: white/ballColor, intensity `1.6`
- Directional key: white, intensity `1.4`
  - Position `(-6,10,8)`
  - Cast shadow
  - 2048 shadow map
  - PCF soft settings
  - Bias `-0.0003`
  - Radius `12`
- Rim directional light: white, intensity `1.25`, position `(8,7,-8)`
- Front directional light: white, intensity `0.4`, position `(0,0,11)`
- Side bounce directional light: white, intensity `0.3`, position `(-9,-2,4)`

## Viewport frustum

```js
let viewportWidth = 10;
let viewportHeight = 6;

const updateFrustumBounds = () => {
  const fovRad = THREE.MathUtils.degToRad(camera.fov);
  viewportHeight = 2 * Math.tan(fovRad / 2) * camera.position.z;
  viewportWidth =
    viewportHeight * (container.clientWidth / container.clientHeight);
};

updateFrustumBounds();

const isMobile = window.innerWidth < 768;
const ballCount = isMobile ? 54 : 96;
```

## Palette definitions

```js
const getDynamicColors = (baseColor) => {
  const hex = baseColor.toLowerCase();

  if (hex === '#e1fc03') {
    return {
      pastel: new THREE.Color('#FBFFE2'),
      light: new THREE.Color('#EFFE69'),
      medium: new THREE.Color('#E1FC03'),
      deep: new THREE.Color('#B1E200'),
      glass: new THREE.Color('#DCFF32')
    };
  }

  if (hex === '#ffc5c2' || hex === '#ffa19e') {
    return {
      pastel: new THREE.Color('#FFF5F4'),
      light: new THREE.Color('#FFECEB'),
      medium: new THREE.Color('#FFA6B3'),
      deep: new THREE.Color('#FF4D6D'),
      glass: new THREE.Color('#FFA6B3')
    };
  }

  if (hex === '#96e5ff') {
    return {
      pastel: new THREE.Color('#F0F9FF'),
      light: new THREE.Color('#C9F1FF'),
      medium: new THREE.Color('#96E5FF'),
      deep: new THREE.Color('#2BA5FF'),
      glass: new THREE.Color('#98E4FF')
    };
  }

  if (hex === '#2f69ff') {
    return {
      pastel: new THREE.Color('#ECEFFF'),
      light: new THREE.Color('#A8C1FF'),
      medium: new THREE.Color('#2F69FF'),
      deep: new THREE.Color('#0A33BF'),
      glass: new THREE.Color('#4D80FF')
    };
  }

  if (hex === '#141414' || hex === '#000000') {
    return {
      pastel: new THREE.Color('#D8D8D8'),
      light: new THREE.Color('#555555'),
      medium: new THREE.Color('#242424'),
      deep: new THREE.Color('#0A0A0A'),
      glass: new THREE.Color('#1F1F1F')
    };
  }

  const c = new THREE.Color(baseColor);
  return {
    pastel: c.clone().offsetHSL(0, -0.15, 0.25),
    light: c.clone().offsetHSL(0, -0.05, 0.12),
    medium: c.clone(),
    deep: c.clone().offsetHSL(0.01, 0.1, -0.12),
    glass: c.clone()
  };
};
```

Scroll morphs:

- Hero palette → volt lime
- Volt lime → rose pink
- Lime blend: `smoothstep(0.55, 1.05, progress)`
- Pink blend: `smoothstep(1.40, 1.95, progress)`

## Sphere creation

Use a shared:

```js
const sphereGeometry = new THREE.SphereGeometry(1, 48, 48);
```

Sphere radius distribution:

- 30%: `0.27 + random * 0.12`
- Next 50%: `0.42 + random * 0.18`
- Last 20%: `0.66 + random * 0.21`
- `mass = radius^3`

Create 96 desktop spheres or 54 mobile spheres.

Glass probability:

- `22%`
- Disabled for black themes

Glass material:

```js
new THREE.MeshPhysicalMaterial({
  color: palette.glass,
  roughness: 0.08,
  metalness: 0.0,
  clearcoat: 1.0,
  clearcoatRoughness: 0.03,
  transmission: 0.95,
  ior: 1.485,
  thickness: 2.2,
  specularColor: new THREE.Color('#ffffff'),
  specularIntensity: 1.0,
  attenuationColor: palette.pastel,
  attenuationDistance: 1.0,
  emissive: palette.glass,
  emissiveIntensity: 0.12,
  transparent: true
});
```

Matte material:

```js
new THREE.MeshPhysicalMaterial({
  color: chosenColor,
  roughness: 0.44,
  metalness: 0.0,
  clearcoat: 0.24,
  clearcoatRoughness: 0.35,
  emissive: isBlack
    ? new THREE.Color('#000000')
    : chosenColor,
  emissiveIntensity: isBlack ? 0.0 : 0.08,
  transparent: true
});
```

Non-glass color distribution:

- 25% pastel
- 30% light
- 30% medium
- 15% deep

Each sphere is held by a `THREE.Group`, with the scaled sphere mesh attached to it.

## Heart silhouette

Use rejection sampling with the classic heart implicit equation:

```js
const inHeart = (x, y) => {
  const a = x * x + y * y - 1;
  return a * a * a - x * x * y * y * y <= 0;
};
```

For every sphere:

- Rejection sample uniformly inside the heart
- x range: `(-1.25, 1.25)`
- y range: `(-1.4, 1.15)`
- `tries` max `60`
- Scale based on viewport:
  `S = Math.min(viewportWidth * 0.30, viewportHeight * 0.27)`
- Base y: `-viewportHeight * 0.03`
- Add small random z depth `(-0.175, 0.175)`
- Recompute targets on resize

## Entrance scatter

Before reveal, park spheres offscreen:

```js
const R = Math.max(viewportWidth, viewportHeight) * 1.5;

for (const b of balls) {
  const angle = Math.random() * Math.PI * 2;
  const px = Math.cos(angle) * R * 1.25;
  const py = Math.sin(angle) * R * 0.85;
  const pz = (Math.random() - 0.5) * 4;
  b.position.set(px, py, pz);
  b.meshGroup.position.copy(b.position);
  b.velocity.set(0, 0, 0);
}
```

On loader reveal, release inward:

```js
b.velocity
  .set(-px, -py, -pz)
  .normalize()
  .multiplyScalar(0.08 + Math.random() * 0.05);
```

## Physics parameters

```js
const params = {
  gravity: 0,
  rebound: -0.3,
  mouseRepelForce: 0.05,
  mouseRepelRadius: 4.4,
  damping: 0.91,
  centerAttractForce: 0.0035,
  bounciness: 0.02
};
```

Additional fixed behavior:

- `mouseRepelRadius * 1.4` while mouse is down
- `mouseRepelForce * 1.7` while mouse is down
- Cursor momentum boost: `1 + mouseSpeed * 3.2`
- Clamp mouse speed to `3`
- Hero damping: `0.91`
- Drop damping: `0.992`
- Shape damping: `0.90`
- Fly damping: `0.985`
- Floor restitution: `0.30 + 0.35 * dropF`
- Collision substeps: `4`
- Collision scale:
  `0.28 * (1 - 0.93 * shapeF) * (1 - flyF)`
- z boundary: `2.0`
- Border padding: `0.2`
- Release walls once `flyF >= 0.5`
- Release z walls once `flyF >= 0.02`

## Scroll animation modes

Helper functions:

```js
const clamp01 = v => v < 0 ? 0 : v > 1 ? 1 : v;

const smoothstep = (a, b, x) => {
  const t = clamp01((x - a) / (b - a));
  return t * t * (3 - 2 * t);
};

const lerp = (a, b, t) => a + (b - a) * t;
const easeOutCubic = t => 1 - Math.pow(1 - t, 3);
```

Mode factors:

- `heroF = 1 - smoothstep(0.30, 0.80, progress)`
- `dropRaw = smoothstep(0.40, 0.95, progress)`
- `flyF = smoothstep(2.7, 3.45, progress)`
- `shapeF = smoothstep(1.40, 2.05, progress) * (1 - smoothstep(2.55, 3.0, progress))`
- `dropF = dropRaw * (1 - smoothstep(1.25, 1.75, progress))`

Entrance attraction:

```js
entranceT = easeOutCubic(clamp01((time - entranceStart) / 2.2));
attractionBoost = lerp(7.5, 1, entranceT);
```

## Animation loop

Maintain:

```js
control = { progress: 0, started: false };
```

On the first frame after `started`:

1. Set local started state
2. Record the entrance start time
3. Scatter spheres and give them inward velocity

Per frame:

1. Update the 3D cursor world position
2. Update scroll-controlled mode factors
3. Morph sphere palette colors
4. Apply hero floating motion
5. Apply cluster attraction
6. Apply drop gravity
7. Apply heart target attraction
8. Apply release acceleration
9. Apply cursor repulsion
10. Integrate positions
11. Update morphing colors and transparency
12. Run pairwise collision substeps
13. Apply viewport walls and floor
14. Rotate spheres based on position change
15. Render the scene

The field:

- Floats in the hero
- Drops and bounces in Physics
- Reassembles into a heart in Form
- Accelerates toward and through the camera in Release
- Fades near the camera at the end

## Mouse projection

Project the normalized cursor into Three.js world space:

```js
const mouseProjVec = new THREE.Vector3();
const mouseWorld3D = new THREE.Vector3();

const updateMouse3D = () => {
  mouseProjVec.set(mousePos.x, mousePos.y, 0.5);
  mouseProjVec.unproject(camera);
  const dir = mouseProjVec.sub(camera.position).normalize();
  const distance = -camera.position.z / dir.z;
  mouseWorld3D.copy(camera.position).add(dir.multiplyScalar(distance));
};
```

## Resize

On resize:

```js
const handleResize = () => {
  const width = container.clientWidth;
  const height = container.clientHeight;
  camera.aspect = width / height;
  camera.updateProjectionMatrix();
  renderer.setSize(width, height);
  updateFrustumBounds();
  assignHeartTargets();
};

new ResizeObserver(handleResize).observe(container);
```

## Loader

Fixed full-screen pre-roll:

- Z-index `100`
- Radial gradient:
  `radial-gradient(circle at 50% 42%, #ffffff 0%, #eef2ff 45%, #dbe4ff 100%)`
- Centered column
- Gap `26px`

Loader elements spring in with staggered entrance:

- Initial delay `0.1s`
- Stagger `0.16s`
- Initial opacity `0`
- Initial y `18`
- Initial blur `6px`
- Final opacity `1`
- Final y `0`
- Final blur `0`
- Spring-like easing

1. Status line:
   - JetBrains Mono 10px
   - Letter spacing `0.28em`
   - Uppercase
   - Color `#6b7bb5`
   - `Calibrating gravity field` while below 100%
   - `Entering orbit` at 100%

2. Progress bar:
   - Width `clamp(180px,32vw,280px)`
   - Height `2px`
   - Track `rgba(14,42,197,0.14)`
   - Fill gradient `#5175ff → #0e2ac5`
   - Glow `0 0 14px rgba(47,105,255,0.55)`
   - Width transition `0.45s cubic-bezier(0.16,1,0.3,1)`

3. Counter:
   - JetBrains Mono 11px
   - Letter spacing `0.1em`
   - Bold
   - Tabular numbers
   - Color `#0e2ac5`
   - Display zero-padded `000%` to `100%`

Loader progress logic:

- Cap loading at 92% until WebGL reports ready
- Ease toward the ceiling:
  `value += (92 - value) * 1.7 * dt`
- Once ready:
  - Ease to 100:
    `value += (100 - value) * 6 * dt`
- Snap to exactly `100` once `value >= 99.4`
- Cap rAF delta time at `0.05`
- At `value >= 99.9` and ready:
  - After `140ms`, set loader to leaving
  - Leaving opacity `0`
  - Pointer events `none`
  - Transition opacity `0.55s cubic-bezier(0.65,0,0.35,1)`
  - After `660ms` total:
    - Set `control.started = true`
    - Reveal the header
    - Reveal hero content
    - Begin the sphere entrance

## Required assets

None.

- No external textures
- No models
- No env maps
- No audio
- No server or API
- Procedural Three.js scene only
- Google Fonts are the only external front-end assets

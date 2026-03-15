---
name: web-presentation
description: Create polished web presentations (slideshows) using pure HTML, CSS, and JavaScript — no frameworks or libraries. Use this skill whenever the user wants to make a presentation, slideshow, pitch deck, lecture slides, or any slide-based content for the browser. Also trigger when users mention "slides", "deck", "presentation", or want to present information in a full-screen slide format, even if they don't explicitly say "web presentation".
---

# Web Presentation Skill

Create beautiful, self-contained web presentations using only HTML, CSS, and JavaScript. The output is a set of three files (HTML, CSS, JS) that work together as a full-screen slideshow in any modern browser.

## Why this approach?

Pure HTML/CSS/JS means zero dependencies, instant loading, works offline, and runs everywhere. The user gets a presentation they can open by double-clicking an HTML file — no build step, no server, no install.

## Output Structure

Generate three separate files in the user's project directory:

```
presentation/
├── index.html    — Slide markup and structure
├── style.css     — All visual styling, transitions, master slide design
└── script.js     — Navigation, fullscreen, slide counter, keyboard/touch logic
```

If the user specifies a different directory or filename, respect that. Otherwise default to a `presentation/` folder.

---

## Slide Master System (CSS-based)

Just like PowerPoint's "Slide Master", define a consistent visual identity across all slides using CSS custom properties and shared layout rules. This ensures brand consistency, typography standards, and uniform spacing across the entire deck.

### CSS Custom Properties (Design Tokens)

Define all design decisions as CSS variables on `:root` so the entire theme is changeable from one place:

```css
:root {
  /* === Color Tokens === */
  --color-bg: #ffffff;
  --color-bg-alt: #f8f9fa;
  --color-text: #1a1a2e;
  --color-heading: #16213e;
  --color-accent: #0f3460;
  --color-accent-secondary: #e94560;
  --color-border: #e0e0e0;
  --color-code-bg: #f4f4f8;

  /* === Typography Tokens === */
  --font-heading: 'Pretendard', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Noto Sans KR', sans-serif;
  --font-body: 'Pretendard', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Noto Sans KR', sans-serif;
  --font-code: 'Fira Code', 'JetBrains Mono', 'D2Coding', monospace;

  --fs-h1: clamp(2rem, 5vw, 3.2rem);
  --fs-h2: clamp(1.6rem, 4vw, 2.4rem);
  --fs-h3: clamp(1.3rem, 3vw, 1.8rem);
  --fs-body: clamp(1rem, 2vw, 1.5rem);
  --fs-small: clamp(0.8rem, 1.5vw, 1rem);
  --fs-counter: 0.85rem;

  --lh-heading: 1.3;
  --lh-body: 1.7;

  /* === Spacing Tokens === */
  --slide-padding: clamp(40px, 6vw, 80px);
  --content-max-width: 1100px;
  --gap-section: 2rem;
  --gap-items: 1.2rem;

  /* === Animation Tokens === */
  --transition-speed: 0.4s;
  --transition-easing: cubic-bezier(0.25, 0.46, 0.45, 0.94);
  --stagger-delay: 0.08s;

  /* === Layout Tokens === */
  --progress-height: 3px;
  --counter-radius: 20px;
  --aspect-ratio: 16 / 9;
}
```

### Dark Theme Override

When the user requests a dark theme:

```css
:root[data-theme="dark"] {
  --color-bg: #0f0f1a;
  --color-bg-alt: #1a1a2e;
  --color-text: #e0e0e0;
  --color-heading: #ffffff;
  --color-accent: #4fc3f7;
  --color-accent-secondary: #ff6b9d;
  --color-border: #2a2a40;
  --color-code-bg: #1e1e2e;
}
```

### Consistent Elements Across All Slides (Master Slide Pattern)

These elements appear on every slide and are controlled centrally:

1. **Slide Number / Counter** — fixed bottom-right, pill-shaped, semi-transparent
2. **Progress Bar** — fixed bottom, full-width, accent-colored
3. **Optional Logo Area** — fixed top-left or top-right for branding (if user provides a logo)
4. **Optional Footer** — fixed bottom-left for date, presenter name, or company

```css
/* === Master slide persistent elements === */
.slide-counter {
  position: fixed;
  bottom: 20px;
  right: 24px;
  font-size: var(--fs-counter);
  color: var(--color-text);
  background: rgba(0, 0, 0, 0.06);
  padding: 4px 14px;
  border-radius: var(--counter-radius);
  z-index: 100;
  font-variant-numeric: tabular-nums;
  backdrop-filter: blur(4px);
}

.progress-bar {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  height: var(--progress-height);
  background: var(--color-border);
  z-index: 100;
}
.progress-fill {
  height: 100%;
  width: 0%;
  background: var(--color-accent);
  transition: width var(--transition-speed) var(--transition-easing);
}

.slide-logo {
  position: fixed;
  top: 20px;
  right: 24px;
  height: 32px;
  opacity: 0.7;
  z-index: 100;
}

.slide-footer {
  position: fixed;
  bottom: 20px;
  left: 24px;
  font-size: var(--fs-small);
  color: var(--color-text);
  opacity: 0.5;
  z-index: 100;
}
```

---

## Slide Layout Types

Provide a variety of slide layout classes, mirroring PowerPoint's standard layouts. Each layout is a CSS class applied to the `.slide` element.

### 1. Title Slide (`.layout-title`)
Full-screen centered title + subtitle. Used for the opening slide.

```html
<div class="slide layout-title active" data-slide="1">
  <div class="slide-content">
    <h1>Presentation Title</h1>
    <p class="subtitle">Subtitle or tagline</p>
    <p class="meta">Author Name · Date</p>
  </div>
</div>
```

```css
.layout-title .slide-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  height: 100%;
}
.layout-title h1 {
  font-size: clamp(2.5rem, 6vw, 4rem);
  font-weight: 800;
  letter-spacing: -0.02em;
  margin-bottom: 0.5em;
}
.layout-title .subtitle {
  font-size: var(--fs-h3);
  color: var(--color-accent);
  margin-bottom: 1em;
}
.layout-title .meta {
  font-size: var(--fs-small);
  opacity: 0.6;
}
```

### 2. Section Header (`.layout-section`)
Introduces a new section. Large centered text with decorative accent.

```html
<div class="slide layout-section" data-slide="N">
  <div class="slide-content">
    <span class="section-number">01</span>
    <h2>Section Title</h2>
    <p class="section-desc">Brief description of this section</p>
  </div>
</div>
```

```css
.layout-section .slide-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
  height: 100%;
}
.layout-section .section-number {
  font-size: var(--fs-h1);
  font-weight: 800;
  color: var(--color-accent);
  opacity: 0.3;
  margin-bottom: 0.5em;
}
.layout-section h2 {
  font-size: var(--fs-h1);
  font-weight: 700;
}
```

### 3. Title + Content (`.layout-content`)
Standard slide: heading on top, content below. The workhorse layout.

```html
<div class="slide layout-content" data-slide="N">
  <div class="slide-content">
    <h2>Slide Heading</h2>
    <ul>
      <li>Point one</li>
      <li>Point two</li>
      <li>Point three</li>
    </ul>
  </div>
</div>
```

```css
.layout-content .slide-content {
  display: flex;
  flex-direction: column;
  justify-content: center;
  height: 100%;
  max-width: var(--content-max-width);
  margin: 0 auto;
}
```

### 4. Two-Column (`.layout-two-col`)
Side-by-side content for comparison or text + image.

```html
<div class="slide layout-two-col" data-slide="N">
  <div class="slide-content">
    <h2>Comparison Title</h2>
    <div class="columns">
      <div class="col">
        <h3>Left Column</h3>
        <p>Content here</p>
      </div>
      <div class="col">
        <h3>Right Column</h3>
        <p>Content here</p>
      </div>
    </div>
  </div>
</div>
```

```css
.layout-two-col .columns {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: var(--gap-section);
  margin-top: var(--gap-section);
}
@media (max-width: 768px) {
  .layout-two-col .columns {
    grid-template-columns: 1fr;
  }
}
```

### 5. Image + Caption (`.layout-image`)
A dominant image with caption text.

```html
<div class="slide layout-image" data-slide="N">
  <div class="slide-content">
    <figure>
      <img src="path/to/image.jpg" alt="Description">
      <figcaption>Caption text explaining the image</figcaption>
    </figure>
  </div>
</div>
```

### 6. Code Slide (`.layout-code`)
Syntax-highlighted code block with heading.

```html
<div class="slide layout-code" data-slide="N">
  <div class="slide-content">
    <h2>Code Example</h2>
    <pre><code class="language-js">function hello() {
  console.log("Hello, World!");
}</code></pre>
  </div>
</div>
```

```css
.layout-code pre {
  background: var(--color-code-bg);
  border-radius: 12px;
  padding: 1.5em 2em;
  overflow-x: auto;
  font-family: var(--font-code);
  font-size: clamp(0.85rem, 1.5vw, 1.1rem);
  line-height: 1.6;
  border: 1px solid var(--color-border);
}
```

### 7. Quote Slide (`.layout-quote`)
Styled blockquote for emphasis.

```html
<div class="slide layout-quote" data-slide="N">
  <div class="slide-content">
    <blockquote>
      <p>"The best way to predict the future is to invent it."</p>
      <cite>— Alan Kay</cite>
    </blockquote>
  </div>
</div>
```

### 8. Blank / Custom (`.layout-blank`)
Empty canvas for fully custom content.

---

## index.html Structure

```html
<!DOCTYPE html>
<html lang="ko" data-theme="light">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Presentation Title]</title>
  <link rel="preconnect" href="https://cdn.jsdelivr.net">
  <link href="https://cdn.jsdelivr.net/gh/orioncactus/pretendard@v1.3.9/dist/web/variable/pretendardvariable-dynamic-subset.min.css" rel="stylesheet">
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="presentation">
    <!-- Slide 1: Title -->
    <div class="slide layout-title active" data-slide="1">
      <div class="slide-content">
        <h1>Presentation Title</h1>
        <p class="subtitle">Subtitle</p>
        <p class="meta">Author · 2026</p>
      </div>
    </div>

    <!-- Slide 2: Content -->
    <div class="slide layout-content" data-slide="2">
      <div class="slide-content">
        <h2>Topic</h2>
        <ul>
          <li>Point A</li>
          <li>Point B</li>
        </ul>
      </div>
    </div>

    <!-- More slides... -->
  </div>

  <!-- Master slide persistent elements -->
  <div class="slide-counter">
    <span class="current-slide">1</span> / <span class="total-slides">N</span>
  </div>

  <div class="progress-bar">
    <div class="progress-fill"></div>
  </div>

  <!-- Optional: logo, footer -->
  <!-- <img class="slide-logo" src="logo.png" alt="Logo"> -->
  <!-- <div class="slide-footer">Company Name · 2026</div> -->

  <script src="script.js"></script>
</body>
</html>
```

Key points:
- Each slide is a `<div class="slide layout-[type]">` with a `data-slide` number
- The first slide gets the `active` class
- Master slide elements (counter, progress bar, logo, footer) sit outside `.presentation` so they're always visible
- Set the `lang` attribute based on the presentation's primary language
- Use Pretendard font for Korean content (CDN-loaded, variable font for performance)

---

## style.css Design

### Base Layout

```css
*, *::before, *::after {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html, body {
  height: 100%;
  overflow: hidden;
  background: var(--color-bg-alt);
}

.presentation {
  position: relative;
  width: 100vw;
  height: 100vh;
}

.slide {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  padding: var(--slide-padding);
  background: var(--color-bg);
  font-family: var(--font-body);
  color: var(--color-text);
  overflow: hidden;
  /* Hidden by default */
  opacity: 0;
  visibility: hidden;
  /* Transition handled by transition classes */
}

.slide.active {
  opacity: 1;
  visibility: visible;
}

.slide-content {
  max-width: var(--content-max-width);
  margin: 0 auto;
  height: 100%;
}
```

### Typography System

```css
h1, h2, h3 {
  font-family: var(--font-heading);
  color: var(--color-heading);
  line-height: var(--lh-heading);
  text-wrap: balance; /* Modern CSS: balanced heading line breaks */
}

h1 { font-size: var(--fs-h1); font-weight: 800; }
h2 { font-size: var(--fs-h2); font-weight: 700; }
h3 { font-size: var(--fs-h3); font-weight: 600; }

p, li {
  font-size: var(--fs-body);
  line-height: var(--lh-body);
  color: var(--color-text);
}

ul, ol {
  padding-left: 1.5em;
}

li + li {
  margin-top: var(--gap-items);
}

strong {
  color: var(--color-accent);
  font-weight: 700;
}

a {
  color: var(--color-accent);
  text-decoration-thickness: 2px;
  text-underline-offset: 3px;
}
```

### Decorative & Visual Details

Add subtle decorative elements to enhance polish:

```css
/* Accent line on content slides */
.layout-content h2::after {
  content: '';
  display: block;
  width: 60px;
  height: 3px;
  background: var(--color-accent);
  margin-top: 0.5em;
  border-radius: 2px;
}

/* Blockquote styling */
blockquote {
  position: relative;
  padding-left: 2em;
  border-left: 4px solid var(--color-accent);
  font-style: italic;
}
blockquote cite {
  display: block;
  margin-top: 1em;
  font-size: var(--fs-small);
  font-style: normal;
  opacity: 0.7;
}

/* Code block syntax theme */
code {
  font-family: var(--font-code);
  background: var(--color-code-bg);
  padding: 0.15em 0.4em;
  border-radius: 4px;
  font-size: 0.9em;
}
```

---

## Transition & Animation System

### Transition Modes

Support multiple transition modes via a `data-transition` attribute on the `<body>` or on individual slides. Default is `fade`.

```css
/* === FADE (default) === */
.slide {
  transition: opacity var(--transition-speed) var(--transition-easing),
              visibility var(--transition-speed);
}
.slide.active {
  opacity: 1;
  visibility: visible;
}

/* === SLIDE LEFT === */
body[data-transition="slide"] .slide {
  transform: translateX(100%);
  transition: transform var(--transition-speed) var(--transition-easing),
              opacity var(--transition-speed);
}
body[data-transition="slide"] .slide.active {
  transform: translateX(0);
  opacity: 1;
  visibility: visible;
}
body[data-transition="slide"] .slide.exit-left {
  transform: translateX(-100%);
  opacity: 0;
}

/* === ZOOM === */
body[data-transition="zoom"] .slide {
  transform: scale(0.8);
  transition: transform var(--transition-speed) var(--transition-easing),
              opacity var(--transition-speed);
}
body[data-transition="zoom"] .slide.active {
  transform: scale(1);
  opacity: 1;
  visibility: visible;
}

/* === NONE === */
body[data-transition="none"] .slide {
  transition: none;
}
```

### Fragment (Step-by-step) Animations

Support PowerPoint-like item-by-item reveal within a slide using `.fragment` class:

```html
<ul>
  <li class="fragment">Appears first</li>
  <li class="fragment">Appears second</li>
  <li class="fragment">Appears third</li>
</ul>
```

```css
.fragment {
  opacity: 0;
  transform: translateY(12px);
  transition: opacity 0.4s ease, transform 0.4s ease;
}
.fragment.visible {
  opacity: 1;
  transform: translateY(0);
}
```

Fragment logic is handled in `script.js` — pressing "next" reveals the next `.fragment` before advancing to the next slide.

### Slide-Enter Staggered Animation

When a slide becomes active, stagger child elements for a polished entrance:

```css
.slide.active .slide-content > * {
  animation: slideEnter 0.5s var(--transition-easing) both;
}
.slide.active .slide-content > *:nth-child(1) { animation-delay: 0s; }
.slide.active .slide-content > *:nth-child(2) { animation-delay: calc(var(--stagger-delay) * 1); }
.slide.active .slide-content > *:nth-child(3) { animation-delay: calc(var(--stagger-delay) * 2); }
.slide.active .slide-content > *:nth-child(4) { animation-delay: calc(var(--stagger-delay) * 3); }
.slide.active .slide-content > *:nth-child(5) { animation-delay: calc(var(--stagger-delay) * 4); }
.slide.active .slide-content > *:nth-child(6) { animation-delay: calc(var(--stagger-delay) * 5); }

@keyframes slideEnter {
  from {
    opacity: 0;
    transform: translateY(16px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

### Accessibility: prefers-reduced-motion

Always respect user motion preferences. Wrap all motion in a reduced-motion media query:

```css
@media (prefers-reduced-motion: reduce) {
  .slide {
    transition: none !important;
  }
  .fragment {
    transition: none !important;
    opacity: 1 !important;
    transform: none !important;
  }
  .slide.active .slide-content > * {
    animation: none !important;
  }
  .progress-fill {
    transition: none !important;
  }
}
```

This ensures users with vestibular disorders or motion sensitivity can still use the presentation without discomfort.

---

## script.js Behavior

### Core State & Navigation

```javascript
(function () {
  'use strict';

  let currentSlide = 1;
  const slides = document.querySelectorAll('.slide');
  const totalSlides = slides.length;

  // Update total count display
  document.querySelector('.total-slides').textContent = totalSlides;

  function goToSlide(n) {
    if (n < 1 || n > totalSlides) return;

    const current = document.querySelector('.slide.active');
    const target = document.querySelector(`[data-slide="${n}"]`);

    if (current === target) return;

    // Handle fragment reset
    current.querySelectorAll('.fragment.visible').forEach(f => f.classList.remove('visible'));

    // Transition classes for directional transitions
    const direction = n > currentSlide ? 'exit-left' : 'exit-right';
    current.classList.add(direction);
    current.classList.remove('active');

    // Clean up transition class after animation
    setTimeout(() => {
      current.classList.remove(direction);
    }, 500);

    target.classList.add('active');
    currentSlide = n;

    // Update UI
    document.querySelector('.current-slide').textContent = currentSlide;
    document.querySelector('.progress-fill').style.width =
      `${(currentSlide / totalSlides) * 100}%`;
  }

  function nextAction() {
    // Check for unrevealed fragments first
    const activeSlide = document.querySelector('.slide.active');
    const hiddenFragment = activeSlide.querySelector('.fragment:not(.visible)');
    if (hiddenFragment) {
      hiddenFragment.classList.add('visible');
      return;
    }
    goToSlide(currentSlide + 1);
  }

  function prevAction() {
    goToSlide(currentSlide - 1);
  }

  // ─── Keyboard Controls ───
  document.addEventListener('keydown', (e) => {
    switch (e.key) {
      case 'ArrowRight':
      case 'ArrowDown':
      case ' ':           // Space bar
      case 'PageDown':
        e.preventDefault();
        nextAction();
        break;
      case 'ArrowLeft':
      case 'ArrowUp':
      case 'PageUp':
        e.preventDefault();
        prevAction();
        break;
      case 'Home':
        e.preventDefault();
        goToSlide(1);
        break;
      case 'End':
        e.preventDefault();
        goToSlide(totalSlides);
        break;
      case 'f':
      case 'F':
        toggleFullscreen();
        break;
      case 'Escape':
        if (document.fullscreenElement) {
          document.exitFullscreen();
        }
        break;
    }
  });

  // ─── Touch / Swipe Support ───
  let touchStartX = 0;
  let touchStartY = 0;
  const SWIPE_THRESHOLD = 50;

  document.addEventListener('touchstart', (e) => {
    touchStartX = e.changedTouches[0].clientX;
    touchStartY = e.changedTouches[0].clientY;
  }, { passive: true });

  document.addEventListener('touchend', (e) => {
    const dx = e.changedTouches[0].clientX - touchStartX;
    const dy = e.changedTouches[0].clientY - touchStartY;

    // Only register horizontal swipes (ignore vertical scroll gestures)
    if (Math.abs(dx) > Math.abs(dy) && Math.abs(dx) > SWIPE_THRESHOLD) {
      if (dx < 0) nextAction();
      else prevAction();
    }
  }, { passive: true });

  // ─── Click Navigation (optional: left/right halves) ───
  document.querySelector('.presentation').addEventListener('click', (e) => {
    // Ignore clicks on interactive elements
    if (e.target.closest('a, button, input, textarea, select, pre, code')) return;

    const x = e.clientX;
    const w = window.innerWidth;
    if (x > w * 0.65) nextAction();
    else if (x < w * 0.35) prevAction();
  });

  // ─── Fullscreen Toggle ───
  function toggleFullscreen() {
    const el = document.documentElement;
    if (!document.fullscreenElement) {
      (el.requestFullscreen || el.webkitRequestFullscreen || el.msRequestFullscreen).call(el);
    } else {
      (document.exitFullscreen || document.webkitExitFullscreen || document.msExitFullscreen).call(document);
    }
  }

  // ─── URL Hash Support ───
  function handleHash() {
    const hash = window.location.hash.replace('#', '');
    const n = parseInt(hash, 10);
    if (n >= 1 && n <= totalSlides) {
      goToSlide(n);
    }
  }
  window.addEventListener('hashchange', handleHash);
  if (window.location.hash) handleHash();

  // ─── Init ───
  document.querySelector('.progress-fill').style.width =
    `${(1 / totalSlides) * 100}%`;
})();
```

### Keyboard Controls

| Key | Action |
|-----|--------|
| `→`, `↓`, `Space`, `PageDown` | Next slide / reveal fragment |
| `←`, `↑`, `PageUp` | Previous slide |
| `Home` | First slide |
| `End` | Last slide |
| `F` | Toggle fullscreen |
| `Escape` | Exit fullscreen |

### Fragment Logic
Pressing "next" first reveals hidden `.fragment` elements one by one within the current slide. Only after all fragments are visible does it advance to the next slide.

### URL Hash Navigation
Support `#3` in the URL to jump directly to slide 3. Useful for sharing links to specific slides.

---

## Responsive Considerations

```css
@media (max-width: 768px) {
  :root {
    --slide-padding: clamp(24px, 4vw, 40px);
  }

  .layout-two-col .columns {
    grid-template-columns: 1fr;
  }

  .slide-counter {
    bottom: 12px;
    right: 12px;
    font-size: 0.75rem;
  }

  .slide-footer {
    display: none;
  }
}

@media (max-width: 480px) {
  :root {
    --slide-padding: 20px;
  }
}
```

Typography uses `clamp()` throughout, so font sizes scale automatically between mobile and desktop without breakpoints.

---

## Print Styles

For exporting to PDF via browser print:

```css
@media print {
  .slide {
    position: relative !important;
    opacity: 1 !important;
    visibility: visible !important;
    page-break-after: always;
    height: auto;
    min-height: 100vh;
  }
  .slide-counter,
  .progress-bar,
  .slide-footer,
  .slide-logo {
    display: none !important;
  }
  .fragment {
    opacity: 1 !important;
    transform: none !important;
  }
}
```

---

## Adapting to User Requests

- **Theme**: If the user asks for a dark theme, add `data-theme="dark"` to `<html>` and include the dark theme CSS variables. If they want a specific brand color, override `--color-accent`.
- **Language**: Set the `lang` attribute and font stack appropriately. For Korean content, ensure Pretendard or 'Noto Sans KR' is in the font stack. For English, consider a distinctive display font.
- **Transitions**: Default is `fade`. Set `data-transition="slide"`, `"zoom"`, or `"none"` on `<body>` if the user requests a specific effect.
- **Complexity**: For simple requests, keep it minimal. For elaborate requests (animations, embedded charts, SVG graphics), add what's needed but keep it vanilla JS/CSS.
- **Number of slides**: Generate as many slides as the content requires. Don't pad with empty slides or truncate meaningful content.
- **Fragments**: Use `.fragment` class on list items or content blocks when the user wants step-by-step reveal (PowerPoint-style "appear" animation).

---

## What NOT to do

- Don't use any external libraries, frameworks, or CDNs (exception: font CDNs like Pretendard/Google Fonts are OK)
- Don't use `<iframe>` embeds unless the user specifically asks
- Don't add speaker notes unless requested
- Don't over-animate — keep transitions purposeful, not decorative
- Don't generate placeholder content like "Lorem ipsum" — if the user gives you content, use it; if they give you a topic, write real content for the slides
- Don't ignore `prefers-reduced-motion` — always include the accessibility media query
- Don't use `display: none` for slide hiding — use `opacity` + `visibility` for smooth transitions

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
├── style.css     — All visual styling
└── script.js     — Navigation, fullscreen, slide counter logic
```

If the user specifies a different directory or filename, respect that. Otherwise default to a `presentation/` folder.

## index.html Structure

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>[Presentation Title]</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="presentation">
    <div class="slide active" data-slide="1">
      <!-- Slide content here -->
    </div>
    <div class="slide" data-slide="2">
      <!-- Slide content here -->
    </div>
    <!-- More slides... -->
  </div>

  <div class="slide-counter">
    <span class="current-slide">1</span> / <span class="total-slides">N</span>
  </div>

  <div class="progress-bar">
    <div class="progress-fill"></div>
  </div>

  <script src="script.js"></script>
</body>
</html>
```

Key points:
- Each slide is a `<div class="slide">` with a `data-slide` number
- The first slide gets the `active` class
- The slide counter sits outside the slides so it's always visible
- A progress bar at the bottom shows how far through the deck the user is
- Set the `lang` attribute based on the presentation's primary language

## Slide Content Guidelines

Each slide should contain meaningful, well-structured content. Think about what makes a good presentation:

- **Title slides**: Large centered title, optional subtitle and author
- **Content slides**: A heading + bullet points, or heading + body text
- **Image slides**: If the user provides images, display them prominently with optional caption
- **Code slides**: Use `<pre><code>` blocks with appropriate styling
- **Two-column slides**: Use a flex layout when the user wants side-by-side content
- **Quote slides**: Styled blockquotes for emphasis

Use semantic HTML inside slides — `<h1>` through `<h3>` for headings, `<ul>`/`<ol>` for lists, `<blockquote>` for quotes, `<figure>` and `<figcaption>` for images.

## style.css Design

Use a clean, professional light theme:

### Color Palette
- Background: `#ffffff` (slides), `#f8f9fa` (body/between slides)
- Primary text: `#1a1a2e`
- Headings: `#16213e`
- Accent color: `#0f3460` (for highlights, links, decorative elements)
- Secondary accent: `#e94560` (for emphasis, important callouts)
- Subtle borders/dividers: `#e0e0e0`

### Typography
- Use system font stack: `-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Noto Sans KR', sans-serif`
- Slide headings: bold, generous size (2.5rem–3rem for h1, scaling down)
- Body text: 1.4rem–1.6rem with comfortable line-height (1.6–1.8)
- Keep text readable from a distance — presentations are meant to be projected

### Layout
- Slides fill the viewport: `100vw × 100vh`
- Center content vertically and horizontally using flexbox
- Add generous padding (60px–80px) so content doesn't touch edges
- Only the `.active` slide is visible; others are hidden
- Smooth transitions when switching slides (a subtle fade or slide, 0.3s–0.4s)

### Slide Counter
- Position: fixed, bottom-right corner
- Small, subtle text (0.9rem) that doesn't distract
- Semi-transparent background pill shape
- Example: `1 / 12`

### Progress Bar
- Position: fixed at the very bottom of the viewport
- Height: 3–4px, full width
- Fill color: accent color
- Width updates proportionally as slides advance

### Responsive Considerations
- Font sizes should scale down slightly on smaller screens
- Padding should reduce on mobile
- The presentation should still be usable on tablets

## script.js Behavior

### Slide Navigation

```javascript
// Track state
let currentSlide = 1;
const totalSlides = document.querySelectorAll('.slide').length;

// Navigate to a specific slide
function goToSlide(n) {
  // Clamp to valid range
  // Remove 'active' from current slide
  // Add 'active' to target slide
  // Update counter and progress bar
  // Update currentSlide
}
```

### Keyboard Controls

| Key | Action |
|-----|--------|
| `ArrowRight`, `ArrowDown` | Next slide |
| `ArrowLeft`, `ArrowUp` | Previous slide |
| `Home` | First slide |
| `End` | Last slide |
| `F` | Toggle fullscreen |

The `F` key should use the Fullscreen API (`document.documentElement.requestFullscreen()`). Handle both entering and exiting fullscreen. Include vendor prefixes for broader compatibility (`webkitRequestFullscreen`, `msRequestFullscreen`).

### Touch Support
Add basic swipe support for mobile/tablet:
- Swipe left → next slide
- Swipe right → previous slide
- Use `touchstart` and `touchend` events with a minimum threshold (50px) to avoid accidental triggers

### Slide Counter Updates
Every time the slide changes, update:
1. The counter text (`currentSlide / totalSlides`)
2. The progress bar width (`(currentSlide / totalSlides) * 100%`)

### Click Navigation
Optionally support clicking the left/right halves of the screen to navigate backward/forward. This is helpful for touchscreen and mouse-only users.

## Transition Effects

Use CSS transitions for slide changes. A simple, professional approach:

```css
.slide {
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.4s ease, visibility 0.4s;
  position: absolute;
  top: 0; left: 0;
  width: 100%; height: 100%;
}
.slide.active {
  opacity: 1;
  visibility: visible;
}
```

Keep transitions subtle — the content is what matters, not the animation.

## Adapting to User Requests

- **Theme**: If the user asks for a dark theme, invert the color scheme (dark backgrounds, light text). If they want a specific brand color, use it as the accent.
- **Language**: Set the `lang` attribute and font stack appropriately. For Korean content, ensure 'Noto Sans KR' or similar is in the font stack.
- **Complexity**: For simple requests, keep it minimal. For elaborate requests (animations, embedded charts), add what's needed but keep it vanilla JS/CSS.
- **Number of slides**: Generate as many slides as the content requires. Don't pad with empty slides or truncate meaningful content.

## What NOT to do

- Don't use any external libraries, frameworks, or CDNs (no reveal.js, no Bootstrap, no jQuery)
- Don't use `<iframe>` embeds unless the user specifically asks
- Don't add speaker notes unless requested
- Don't over-animate — one transition effect is enough
- Don't generate placeholder content like "Lorem ipsum" — if the user gives you content, use it; if they give you a topic, write real content for the slides

# Lesson 7: Animations and Hover Effects

## Mission Brief
A real command console doesn't just sit still — buttons glow when you approach them, panels slide smoothly into place, indicators pulse to catch your eye. Motion is what makes an interface feel alive and responsive to the user. Today, Navigator, you'll add transitions, hover states, and keyframe animations to bring your page to life without writing a single line of JavaScript.

## Learning Objectives
- Use `transition` to smoothly animate property changes
- Apply `:hover`, `:focus`, and `:active` pseudo-classes purposefully
- Write custom `@keyframes` animations and apply them with `animation`
- Understand `transform` (translate, scale, rotate) for performant motion
- Recognize accessibility considerations around motion (`prefers-reduced-motion`)

## What You'll Build
Polished hover effects on your nav links and project cards, plus a subtle looping `@keyframes` animation (a "twinkling star" or pulsing badge) added to your Crew Profile page.

## Prerequisites
Lessons 3-5: selectors, the box model, Flexbox/Grid layouts to animate.

## Key Concepts
- `transition` (property, duration, timing-function)
- `:hover`, `:focus`, `:active` pseudo-classes
- `transform`: `translate()`, `scale()`, `rotate()`
- `@keyframes` and the `animation` property
- `prefers-reduced-motion`

## Concept Explanation
CSS **transitions** let a property change smoothly over time instead of jumping instantly. You define which property to animate, how long it should take, and how its speed should curve, using the `transition` property: `transition: transform 0.3s ease;` means "whenever `transform` changes on this element, animate that change over 0.3 seconds using an `ease` (start slow, speed up, end slow) timing curve." Transitions require a *state change* to trigger them — most commonly a pseudo-class like `:hover` (mouse over), `:focus` (keyboard/tab focus, essential for accessibility — never remove focus outlines without replacing them with something equally visible), or `:active` (during a click).

For the actual visual change itself, the `transform` property is strongly preferred over animating `width`, `height`, `top`, or `left` directly, because transforms (`translate()` for moving, `scale()` for resizing, `rotate()` for rotating) are handled by the browser's compositor and GPU — meaning they animate far more smoothly (no layout recalculation) than properties that force the browser to reflow the page on every frame.

For more elaborate, multi-step, or looping motion — not just a simple A-to-B transition — CSS provides `@keyframes`. You define named stages of an animation as percentages (or `from`/`to`) of its total duration, then apply it to an element with the `animation` shorthand property (name, duration, timing-function, iteration-count, and more):

```css
@keyframes pulse {
  0%   { transform: scale(1); opacity: 1; }
  50%  { transform: scale(1.15); opacity: 0.8; }
  100% { transform: scale(1); opacity: 1; }
}

.badge {
  animation: pulse 2s ease-in-out infinite;
}
```

Motion should always serve a purpose — drawing attention, signaling interactivity, giving feedback — not simply exist for its own sake, and it should never be so aggressive that it becomes distracting or, for some users, physically uncomfortable. Some visitors experience motion sickness or vestibular disorders triggered by animation; the `prefers-reduced-motion` media feature lets you detect when a user has requested less motion at the OS level, so you can disable or tone down non-essential animations for them:

```css
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

## Guided Coding
```css
/* Smooth, purposeful nav link hover */
nav a {
  color: #a6c1ff;
  text-decoration: none;
  font-weight: 600;
  padding-bottom: 2px;
  border-bottom: 2px solid transparent;
  transition: border-color 0.25s ease, color 0.25s ease;
}

nav a:hover,
nav a:focus {
  color: #ffffff;
  border-bottom-color: #ffffff;
}

/* Project card lift-and-glow on hover */
.project-card {
  transition: transform 0.25s ease, box-shadow 0.25s ease;
}

.project-card:hover {
  transform: translateY(-6px) scale(1.02);
  box-shadow: 0 12px 24px rgba(26, 26, 46, 0.15);
}

/* A gently pulsing "status: online" badge */
.status-badge {
  display: inline-block;
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background-color: #2ecc71;
  animation: pulse 2s ease-in-out infinite;
}

@keyframes pulse {
  0%   { transform: scale(1);    opacity: 1;   }
  50%  { transform: scale(1.4);  opacity: 0.6; }
  100% { transform: scale(1);    opacity: 1;   }
}

/* Respect users who have requested reduced motion */
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

## Code Walkthrough
- `nav a { transition: border-color 0.25s ease, color 0.25s ease; }` prepares two properties to animate smoothly; nothing visually happens until a state (like `:hover`) actually changes those properties.
- `nav a:hover, nav a:focus` — styling both together ensures keyboard users tabbing through links get the *same* visible feedback as mouse users hovering, which is an accessibility requirement, not an optional nicety.
- `.project-card:hover { transform: translateY(-6px) scale(1.02); box-shadow: ...; }` — combines a slight lift, a slight scale-up, and a soft shadow for a satisfying "lift off the page" feel, all GPU-accelerated via `transform`.
- `@keyframes pulse { 0% ... 50% ... 100% ... }` defines three snapshots across the animation's duration; the browser smoothly interpolates between them.
- `.status-badge { animation: pulse 2s ease-in-out infinite; }` applies the keyframes: 2 second duration, ease-in-out curve, repeating forever.
- The `prefers-reduced-motion` block uses `!important` deliberately here to guarantee it overrides any other animation/transition durations elsewhere in the stylesheet for users who need it.

## Student Mission
1. Add the nav hover/focus transition to your Crew Profile page and confirm both mouse hover AND keyboard Tab-focus show the same visual feedback.
2. Add the project-card lift-and-glow hover effect to your gallery cards from Lesson 5.
3. Add a pulsing status badge (e.g. next to "Available for missions") using the `@keyframes` pattern above.
4. Add the `prefers-reduced-motion` media query block to respect user motion preferences.

## Challenge
Create a new `@keyframes` animation that makes a small decorative element (e.g. a "rocket" emoji or icon) slide in from off-screen and fade in when the page loads, using `transform: translateX()` and `opacity` across at least 3 keyframe stops, applied once (`animation-iteration-count: 1`, not infinite).

## Experiment/Extension
Change your `.project-card:hover` transition's timing function from `ease` to `linear`, then to `cubic-bezier(0.68, -0.55, 0.27, 1.55)` (a bouncy overshoot curve). Note in a comment how differently each one *feels*, even though the start and end states are identical.

## Common Mistakes
- Animating `width`/`height`/`top`/`left` directly instead of using `transform`, causing janky, slow-feeling motion.
- Styling only `:hover` and forgetting `:focus`, leaving keyboard users with no visible feedback (an accessibility bug).
- Overusing `infinite` looping animations on too many elements, creating a distracting, chaotic page.
- Forgetting to set a `transition` on the base state — without it, hover changes will jump instantly instead of animating.
- Ignoring `prefers-reduced-motion`, which can cause real discomfort for some users.

## Debugging Tips
- DevTools' Elements panel lets you force-toggle pseudo-classes like `:hover` and `:focus` (right-click an element → "Force state") so you can inspect hover styles without holding your mouse in place.
- The Chrome DevTools "Animations" panel lets you slow down, pause, and scrub through running CSS animations frame by frame — extremely useful for tuning keyframe timing.
- If a transition "isn't working," check that the property is actually listed in your `transition` declaration — `transition: color 0.2s;` will NOT animate a `transform` change, for example.

## Check Your Understanding
1. What's the difference between a `transition` and a `@keyframes` animation?
2. Why is `transform` generally preferred over animating `width`/`top`/`left`?
3. Why should `:focus` styles usually match `:hover` styles?
4. What does `animation-iteration-count: infinite` do?
5. What is `prefers-reduced-motion` for, and why does it matter?

## Mini Quiz + Answer Key
**Quiz**
1. Which property defines how long a transition takes?
   a) `transition-property`  b) `transition-duration`  c) `transition-timing`  d) `animation-speed`
2. True or False: `@keyframes` can only have a start and end state.
3. Which transform function moves an element without affecting layout flow?
4. What pseudo-class shows feedback for keyboard-focused elements?
5. What media feature respects users who want less motion?

**Answer Key**
1. b) `transition-duration`
2. False — keyframes can define any number of percentage stops.
3. `:focus`
4. It repeats the animation forever.
5. `prefers-reduced-motion`

## Lesson Recap
- `transition` smoothly animates a property change triggered by a state like `:hover` or `:focus`.
- `transform` (translate/scale/rotate) is the performant way to animate motion.
- `@keyframes` + `animation` create multi-step or looping animations.
- `:hover` and `:focus` should generally be styled together for accessibility.
- `prefers-reduced-motion` lets you respect users sensitive to motion.

## Homework
Add at least three distinct hover/transition effects and one `@keyframes` animation to your Crew Profile page that didn't exist before, and include a `prefers-reduced-motion` block covering all of them.

## Portfolio Project Connection
These hover states, transitions, and keyframe animations become the interactive polish layer across **all three projects** — button/link hover states in Project 01, the animated hamburger icon in Project 02, and hover effects on the product catalog cards in Project 03.

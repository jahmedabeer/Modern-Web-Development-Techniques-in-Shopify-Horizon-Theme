# Complete List of Modern Web Development Techniques in Shopify Horizon Theme

## Overview

The Shopify Horizon theme showcases cutting-edge web development techniques and represents the forefront of modern web standards implementation. This document catalogs all the advanced features and modern APIs used throughout the theme.

## Table of Contents

1. [View Transitions API](#1-view-transitions-api)
2. [CSS Container Queries](#2-css-container-queries)
3. [ES Modules (Import/Export)](#3-es-modules-importexport)
4. [CSS Nesting](#4-css-nesting)
5. [Custom Elements (Web Components)](#5-custom-elements-web-components)
6. [CSS `interpolate-size` Property](#6-css-interpolate-size-property)
7. [CSS `@starting-style` Rule](#7-css-starting-style-rule)
8. [Modern CSS Color Functions](#8-modern-css-color-functions)
9. [Intersection Observer API](#9-intersection-observer-api)
10. [ResizeObserver API](#10-resizeobserver-api)
11. [CSS Logical Properties](#11-css-logical-properties)
12. [CSS Scroll Snap](#12-css-scroll-snap)
13. [CSS Custom Properties (CSS Variables)](#13-css-custom-properties-css-variables)
14. [CSS Grid with Advanced Features](#14-css-grid-with-advanced-features)
15. [Modern Event Handling](#15-modern-event-handling)
16. [CSS `content-visibility`](#16-css-content-visibility)
17. [AbortController API](#17-abortcontroller-api)
18. [CSS `:has()` Selector](#18-css-has-selector)

---

## 1. View Transitions API

**Browser Support**: Chrome 111+, Edge 111+ (experimental in other browsers)

### Implementation
Full-featured page transitions with custom transition types for seamless navigation experiences.

### Code Examples

**JavaScript Implementation**:
```javascript
// assets/view-transitions.js
window.addEventListener('pageswap', async (event) => {
  if (!hasViewTransition(event)) return;
  const { viewTransition } = event;
  
  viewTransition.types.clear();
  viewTransition.types.add('page-navigation');
});
```

**CSS Implementation**:
```css
/* assets/base.css */
@view-transition {
  navigation: auto;
}

html:active-view-transition-type(page-navigation) main[data-page-transition-enabled='true'] {
  view-transition-name: main-content;
}

::view-transition-old(main-content) {
  animation: var(--view-transition-old-main-content);
}
```

### Documentation
- [MDN View Transitions API](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API)
- [Chrome Developers Guide](https://developer.chrome.com/docs/web-platform/view-transitions/)

---

## 2. CSS Container Queries

**Browser Support**: Chrome 105+, Firefox 110+, Safari 16+

### Implementation
Responsive design based on container size rather than viewport, enabling truly modular components.

### Code Examples

```css
/* snippets/product-grid.liquid */
@container product-grid (width < calc(200px * 3 + 16px * 2)) {
  .product-grid__item { 
    grid-column: span 2; 
  }
}

/* assets/base.css */
@container (max-width: 70px) {
  .card-gallery:hover .quick-add__button {
    display: none;
  }
}

/* snippets/cart-products.liquid */
@container cart-items (width >= 720px) {
  .cart-item { 
    flex-direction: row; 
  }
}

/* blocks/accordion.liquid - Style queries */
@container style(--border-width: 0) or style(--border-style: none) {
  .accordion { border: none; }
}
```

### Documentation
- [MDN Container Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Container_Queries)
- [CSS Working Group Spec](https://www.w3.org/TR/css-contain-3/)

---

## 3. ES Modules (Import/Export)

**Browser Support**: All modern browsers

### Implementation
Modern JavaScript module system with clean imports/exports and `type="module"` scripts.

### Code Examples

**Module Exports**:
```javascript
// assets/slideshow.js
import { Component } from '@theme/component';
import { center, closest, clamp, getVisibleElements } from '@theme/utilities';
import { Scroller, scrollIntoView } from '@theme/scrolling';

export class Slideshow extends Component {
  // Component implementation
}

// assets/events.js
export class SlideshowSelectEvent extends Event {
  constructor({ index, previousIndex, userInitiated, trigger, slide, id }) {
    super('slideshowSelect', { bubbles: true });
    this.detail = { index, previousIndex, userInitiated, trigger, slide, id };
  }
}
```

**HTML Module Loading**:
```html
<!-- snippets/scripts.liquid -->
<script src="{{ 'slideshow.js' | asset_url }}" type="module"></script>
<script src="{{ 'product-form.js' | asset_url }}" type="module"></script>
<script src="{{ 'accordion-custom.js' | asset_url }}" type="module"></script>
```

### Documentation
- [MDN ES Modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules)
- [V8 ES Modules Guide](https://v8.dev/features/modules)

---

## 4. CSS Nesting

**Browser Support**: Chrome 112+, Firefox 117+, Safari 16.5+

### Implementation
Native CSS nesting without preprocessors, improving code organization and maintainability.

### Code Examples

```css
/* assets/base.css */
.dialog-modal {
  border: none;
  box-shadow: var(--shadow-popover);

  @media screen and (width >= 750px) {
    border-radius: var(--style-border-radius-popover);
    max-width: var(--normal-content-width);
  }

  &[open] {
    opacity: 1;
  }

  &::backdrop {
    background: transparent;
  }
}

.custom-typography {
  h1 {
    line-height: var(--line-height--display, var(--line-height));
  }

  h2, h3, h4 {
    line-height: var(--line-height--heading, var(--line-height));
  }

  p {
    line-height: var(--line-height--body, var(--line-height));
  }
}

.slideshow-slides {
  scroll-snap-type: x mandatory;
  scroll-behavior: smooth;

  @media (prefers-reduced-motion) {
    scroll-behavior: auto;
  }

  &::-webkit-scrollbar {
    width: 0;
  }
}
```

### Documentation
- [MDN CSS Nesting](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Nesting)
- [W3C CSS Nesting Spec](https://www.w3.org/TR/css-nesting-1/)

---

## 5. Custom Elements (Web Components)

**Browser Support**: All modern browsers

### Implementation
Extensive use of custom elements for reusable, encapsulated components.

### Code Examples

```javascript
// assets/slideshow.js
export class Slideshow extends Component {
  requiredRefs = ['scroller'];
  
  async connectedCallback() {
    super.connectedCallback();
    // Setup slideshow functionality
    this.#scroll = new Scroller(this.refs.scroller, {
      onScroll: this.#handleScroll,
      onScrollStart: this.#onTransitionInit,
      onScrollEnd: this.#onTransitionEnd,
    });
  }

  disconnectedCallback() {
    super.disconnectedCallback();
    this.#scroll?.destroy();
  }
}

if (!customElements.get('slideshow-component')) {
  customElements.define('slideshow-component', Slideshow);
}

// assets/accordion-custom.js
class AccordionCustom extends HTMLElement {
  #controller = new AbortController();

  connectedCallback() {
    this.summary.addEventListener('click', this.handleClick, {
      signal: this.#controller.signal
    });
  }

  disconnectedCallback() {
    this.#controller.abort();
  }
}

if (!customElements.get('accordion-custom')) {
  customElements.define('accordion-custom', AccordionCustom);
}
```

### Documentation
- [MDN Custom Elements](https://developer.mozilla.org/en-US/docs/Web/Web_Components/Using_custom_elements)
- [HTML Living Standard](https://html.spec.whatwg.org/multipage/custom-elements.html)

---

## 6. CSS `interpolate-size` Property

**Browser Support**: Chrome 129+ (experimental)

### Implementation
Smooth animations between `auto` and fixed sizes, eliminating common animation limitations.

### Code Examples

```css
/* assets/base.css */
accordion-custom details {
  &::details-content,
  .details-content {
    block-size: 0;
    overflow-y: clip;
    opacity: 0;
    interpolate-size: allow-keywords;
    transition: 
      content-visibility var(--animation-speed-slow) allow-discrete,
      padding-block var(--animation-speed-slow) var(--animation-easing),
      opacity var(--animation-speed-slow) var(--animation-easing),
      block-size var(--animation-speed-slow) var(--animation-easing);
  }

  &[open] {
    &::details-content,
    .details-content {
      block-size: auto;
      opacity: 1;
    }
  }
}

/* snippets/quick-add.liquid */
.quick-add-modal-content {
  interpolate-size: allow-keywords;
  transition: block-size var(--animation-speed) var(--animation-easing);
}

/* snippets/sorting.liquid */
.facets__disclosure {
  interpolate-size: allow-keywords;
  transition: block-size var(--animation-speed) var(--animation-easing);
}
```

### Documentation
- [CSS Working Group Draft](https://drafts.csswg.org/css-sizing-4/#interpolate-size)
- [Chrome Platform Status](https://chromestatus.com/feature/5207742073856000)

---

## 7. CSS `@starting-style` Rule

**Browser Support**: Chrome 117+, Safari 17.4+

### Implementation
Define starting styles for elements entering the DOM, enabling smooth entry animations.

### Code Examples

```css
/* assets/base.css */
results-list[initialized] .product-grid__item {
  transition: 
    opacity var(--animation-speed) var(--animation-easing),
    transform var(--animation-speed) var(--animation-easing);

  @starting-style {
    opacity: 0;
    transform: translateY(10px);
  }
}

.close-button {
  opacity: 0;
  animation: elementSlideInBottom var(--animation-speed) var(--animation-easing) forwards;

  @starting-style {
    opacity: 0;
    transform: translateY(var(--padding-sm));
  }
}

/* sections/password.liquid */
.password-form {
  opacity: 1;
  transform: translateY(0);
  transition: opacity 0.3s ease, transform 0.3s ease;

  @starting-style {
    opacity: 0;
    transform: translateY(20px);
  }
}

/* snippets/facets-actions.liquid */
.facets__active {
  opacity: 1;
  transition: opacity var(--animation-speed) var(--animation-easing);

  @starting-style {
    opacity: 0;
  }
}
```

### Documentation
- [MDN @starting-style](https://developer.mozilla.org/en-US/docs/Web/CSS/@starting-style)
- [CSS Transitions Level 2 Spec](https://drafts.csswg.org/css-transitions-2/#starting-style)

---

## 8. Modern CSS Color Functions

**Browser Support**: Chrome 111+, Firefox 113+, Safari 16.4+

### Implementation
`rgb()` and `rgba()` with `from` keyword for sophisticated color manipulation.

### Code Examples

```css
/* assets/base.css */
.unit-price {
  color: rgb(from var(--color-foreground) r g b / var(--opacity-subdued-text));
}

.dialog-modal::backdrop {
  background: rgba(from var(--backdrop-color) r g b / var(--backdrop-opacity));
}

.product-grid__item {
  border: var(--product-card-border-width) solid
    rgba(from var(--color-border) r g b / var(--product-card-border-opacity));
}

.search-input::placeholder {
  color: rgba(from var(--color-foreground) r g b / var(--opacity-subdued-text));
}

.tax-note,
.compare-at-price {
  color: rgb(from var(--color-foreground) r g b / var(--opacity-subdued-text));
}

/* Scrollbar theming */
html {
  scrollbar-color: rgb(from var(--color-foreground) r g b / 40%) var(--color-background);
}
```

### Documentation
- [MDN Relative Color Syntax](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Colors/Relative_colors)
- [CSS Color Module Level 5](https://www.w3.org/TR/css-color-5/)

---

## 9. Intersection Observer API

**Browser Support**: All modern browsers

### Implementation
Performance-optimized visibility detection for scroll effects and lazy loading.

### Code Examples

```javascript
// assets/header.js
this.#intersectionObserver = new IntersectionObserver(([entry]) => {
  if (!entry) return;
  const { isIntersecting } = entry;
  
  if (alwaysSticky) {
    this.dataset.stickyState = isIntersecting ? 'inactive' : 'active';
    changeMetaThemeColor(this.refs.headerRowTop);
  } else {
    this.#offscreen = !isIntersecting || this.dataset.stickyState === 'active';
  }
}, {
  root: null,
  rootMargin: '0px',
  threshold: 0
});

// assets/utilities.js
const getIntersectingElements = async (elements, options = {}) => {
  return new Promise((resolve) => {
    const observer = new IntersectionObserver((entries) => {
      const intersecting = entries
        .filter(entry => entry.isIntersecting)
        .map(entry => entry.target);
      resolve(intersecting);
      observer.disconnect();
    }, {
      rootMargin: `0px 0px 1000px 0px`,
      ...options
    });
    
    elements.forEach(el => observer.observe(el));
  });
};

// View transition setup for visible product cards
const viewTransitionTypes = {
  'product-grid': async () => {
    const productCards = document.querySelectorAll('.product-grid .product-grid__item');
    if (!productCards.length) return;
    
    const visibleProductCards = await getIntersectingElements(productCards);
    visibleProductCards.forEach((card) =>
      card.style.setProperty('view-transition-name', `product-card-${card.dataset.productId}`)
    );
  }
};
```

### Documentation
- [MDN Intersection Observer](https://developer.mozilla.org/en-US/docs/Web/API/Intersection_Observer_API)
- [W3C Intersection Observer Spec](https://www.w3.org/TR/intersection-observer/)

---

## 10. ResizeObserver API

**Browser Support**: All modern browsers

### Implementation
Responsive component behavior that adapts to container size changes.

### Code Examples

```javascript
// assets/slideshow.js
this.#resizeObserver = new ResizeObserver(async () => {
  if (viewTransition.current) await viewTransition.current;
  
  if (visibleSlidesAmount > 1) {
    this.#updateVisibleSlides();
  }
  
  if (this.hasAttribute('auto-hide-controls')) {
    this.#updateControlsVisibility();
  }
});

this.#resizeObserver.observe(this.refs.slideshowContainer);

// assets/header.js
this.#resizeObserver = new ResizeObserver(() => {
  calculateHeaderGroupHeight(header, headerGroup);
});

this.#resizeObserver.observe(this);

// assets/critical.js - Custom ResizeNotifier class
class ResizeNotifier extends ResizeObserver {
  #hasInitialized = new WeakSet();

  constructor(callback) {
    super((entries, observer) => {
      const filteredEntries = entries.filter((entry) => {
        if (this.#hasInitialized.has(entry.target)) {
          return true;
        } else {
          this.#hasInitialized.add(entry.target);
          return false;
        }
      });

      if (filteredEntries.length > 0) {
        callback(filteredEntries, observer);
      }
    });
  }
}
```

### Documentation
- [MDN ResizeObserver](https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver)
- [W3C ResizeObserver Spec](https://www.w3.org/TR/resize-observer-1/)

---

## 11. CSS Logical Properties

**Browser Support**: All modern browsers

### Implementation
Writing-mode independent styling for international layouts.

### Code Examples

```css
/* assets/base.css */
.product-grid__link {
  padding-block: var(--padding-block-start) var(--padding-block-end);
  padding-inline: var(--padding-inline-start) var(--padding-inline-end);
}

.spacing-style {
  padding-block: calc(var(--padding-block-start) + var(--section-top-offset, 0px)) var(--padding-block-end);
  padding-inline: var(--padding-inline-start) var(--padding-inline-end);
  margin-block: var(--margin-block-start) var(--margin-block-end);
  margin-inline: var(--margin-inline-start) var(--margin-inline-end);
}

.section {
  padding-block: var(--section-padding-block-start) var(--section-padding-block-end);
  padding-inline: var(--section-padding-inline-start) var(--section-padding-inline-end);
}

/* Typography spacing */
:is(p, h1, h2, h3, h4, h5, h6):first-child,
:empty:first-child + :where(p, h1, h2, h3, h4, h5, h6) {
  margin-block-start: 0;
}

:is(p, h1, h2, h3, h4, h5, h6):last-child {
  margin-block-end: 0;
}

/* Scrollbar positioning */
slideshow-slides[gutters*='start'] {
  padding-inline-start: var(--gutter-slide-width, 0);
  scroll-padding-inline-start: var(--gutter-slide-width, 0);
}

slideshow-slides[gutters*='end'] {
  padding-inline-end: var(--gutter-slide-width, 0);
  scroll-padding-inline-end: var(--gutter-slide-width, 0);
}
```

### Documentation
- [MDN CSS Logical Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Logical_Properties)
- [CSS Logical Properties Spec](https://www.w3.org/TR/css-logical-1/)

---

## 12. CSS Scroll Snap

**Browser Support**: All modern browsers

### Implementation
Smooth scrolling behavior for slideshows and carousels with accessibility considerations.

### Code Examples

```css
/* assets/base.css */
.slideshow-slides {
  display: flex;
  overflow-x: scroll;
  scroll-snap-type: x mandatory;
  scroll-behavior: smooth;
  scrollbar-color: transparent transparent;
  scrollbar-width: none;
  gap: var(--slideshow-gap, 0);

  @media (prefers-reduced-motion) {
    scroll-behavior: auto;
  }

  &::-webkit-scrollbar {
    width: 0;
  }
}

slideshow-slide {
  scroll-snap-align: start;
  flex-shrink: 0;
}

/* Smooth scrolling with accessibility */
html {
  scroll-behavior: smooth;

  @media (prefers-reduced-motion: reduce) {
    scroll-behavior: auto;
  }
}

.dialog-modal {
  scrollbar-width: none;

  @media (prefers-reduced-motion: no-preference) {
    scroll-behavior: smooth;
  }
}
```

### Documentation
- [MDN CSS Scroll Snap](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Scroll_Snap)
- [CSS Scroll Snap Module](https://www.w3.org/TR/css-scroll-snap-1/)

---

## 13. CSS Custom Properties (CSS Variables)

**Browser Support**: All modern browsers

### Implementation
Dynamic theming system with sophisticated variable inheritance and calculations.

### Code Examples

```css
/* assets/base.css */
:root {
  /* Animation system */
  --hover-lift-amount: 4px;
  --hover-scale-amount: 1.03;
  --hover-subtle-zoom-amount: 1.015;
  --hover-shadow-color: var(--color-shadow);
  --hover-transition-duration: 0.25s;
  --hover-transition-timing: ease-out;
  
  /* Animation timing */
  --animation-speed: 0.25s;
  --animation-speed-slow: 0.5s;
  --animation-speed-medium: 0.15s;
  --animation-easing: cubic-bezier(0.4, 0.0, 0.2, 1);
  --animation-timing-bounce: cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

/* Dynamic button theming */
.button {
  color: var(--button-color);
  background-color: var(--button-background-color);
  border-radius: var(--style-border-radius-buttons-primary);
  transition: 
    color var(--animation-speed) var(--animation-easing),
    background-color var(--animation-speed) var(--animation-easing),
    transform var(--hover-transition-duration) var(--hover-transition-timing);
}

/* Responsive spacing system */
.spacing-style {
  --gap-scale: var(--spacing-scale-md);
  gap: calc(var(--gap-scale) * var(--spacing-base));
}

/* Section height system */
.section--height-small {
  --section-min-height: var(--section-height-small);
}

.section--height-full-screen {
  --section-min-height: 100svh;
}

.section-content-wrapper {
  min-height: calc(var(--section-min-height, auto) - var(--section-height-offset, 0px));
}

/* Slideshow customization */
.slideshow-slides {
  gap: var(--slideshow-gap, 0);
  cursor: var(--cursor);
}
```

### Documentation
- [MDN CSS Custom Properties](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)
- [CSS Custom Properties Spec](https://www.w3.org/TR/css-variables-1/)

---

## 14. CSS Grid with Advanced Features

**Browser Support**: All modern browsers (subgrid: Firefox 71+, Safari 16+, Chrome 117+)

### Implementation
Advanced grid layouts with named lines and sophisticated responsive behavior.

### Code Examples

```css
/* assets/base.css */
.product-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: var(--product-grid-gap);
  margin: auto;
  padding: 0;
  list-style: none;

  @media screen and (width >= 750px) {
    grid-template-columns: var(--product-grid-columns-desktop);
  }
}

/* Advanced grid with named lines */
.section {
  display: grid;
  grid-template-columns: 
    [full-start] var(--page-margin) 
    [content-start] 1fr 
    [content-end] var(--page-margin) 
    [full-end];
}

.section > * {
  grid-column: content;
}

.section > .force-full-width {
  grid-column: full;
}

/* Responsive grid behavior */
.product-grid-container {
  display: block;
  width: 100%;
  padding-block: var(--padding-block-start) var(--padding-block-end);

  @media screen and (width >= 750px) {
    display: grid;
  }
}

/* Mobile-first responsive grid */
[product-grid-view='mobile-single'],
.product-grid-mobile--large {
  @media screen and (width < 750px) {
    grid-template-columns: 1fr;
  }
}
```

### Documentation
- [MDN CSS Grid](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout)
- [MDN CSS Subgrid](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Subgrid)

---

## 15. Modern Event Handling

**Browser Support**: All modern browsers

### Implementation
Custom events, event delegation, and sophisticated event management patterns.

### Code Examples

```javascript
// assets/events.js - Custom Event Classes
export class SlideshowSelectEvent extends Event {
  constructor({ index, previousIndex, userInitiated, trigger, slide, id }) {
    super('slideshowSelect', { bubbles: true });
    this.detail = { index, previousIndex, userInitiated, trigger, slide, id };
  }
}

export class CartUpdateEvent extends Event {
  constructor(resource, sourceId, data) {
    super(ThemeEvents.cartUpdate, { bubbles: true });
    this.detail = {
      resource,
      sourceId,
      data: { ...data }
    };
  }
}

export class VariantUpdateEvent extends Event {
  constructor(resource, previousResource, sourceId, data = {}) {
    super(ThemeEvents.variantUpdate, { bubbles: true });
    this.detail = {
      resource,
      previousResource,
      sourceId,
      data: { ...data }
    };
  }
}

// Event usage in components
// assets/slideshow.js
this.dispatchEvent(
  new SlideshowSelectEvent({
    index: newIndex,
    previousIndex: currentIndex,
    userInitiated: true,
    trigger: 'drag',
    slide: newSlide,
    id: newSlide.getAttribute('slide-id'),
  })
);

// assets/component-cart-items.js - Event delegation
connectedCallback() {
  super.connectedCallback();
  document.addEventListener(ThemeEvents.cartUpdate, this.#handleCartUpdate);
  document.addEventListener(ThemeEvents.discountUpdate, this.handleDiscountUpdate);
  document.addEventListener(ThemeEvents.quantitySelectorUpdate, this.#debouncedOnChange);
}

disconnectedCallback() {
  super.disconnectedCallback();
  document.removeEventListener(ThemeEvents.cartUpdate, this.#handleCartUpdate);
  document.removeEventListener(ThemeEvents.quantitySelectorUpdate, this.#debouncedOnChange);
}

// assets/local-pickup.js - Scoped event handling
connectedCallback() {
  super.connectedCallback();
  const closestSection = this.closest(`.shopify-section, dialog`);

  const variantUpdated = (event) => {
    const variantId = event.detail.resource ? event.detail.resource.id : null;
    if (variantId !== this.dataset.variantId) {
      this.#fetchAvailability(variantId);
    }
  };

  closestSection?.addEventListener(ThemeEvents.variantUpdate, variantUpdated);
  
  this.disconnectedCallback = () => {
    closestSection?.removeEventListener(ThemeEvents.variantUpdate, variantUpdated);
  };
}
```

### Documentation
- [MDN CustomEvent](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent)
- [MDN Event Handling](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)

---

## 16. CSS `content-visibility`

**Browser Support**: Chrome 85+, Firefox 125+

### Implementation
Performance optimization for off-screen content rendering.

### Code Examples

```css
/* assets/base.css */
/* 
 * Slides adjacent to the current slide are likely to be scrolled to soon, so
 * try to kick load them beforehand (they are lazy loaded otherwise).
 */
:is(.collection-list__carousel, .resource-list__carousel, .card-gallery)
  :is(
    slideshow-slide:has(+ slideshow-slide[aria-hidden='false']),
    slideshow-slide[aria-hidden='false'] + slideshow-slide
  ) {
  content-visibility: auto;

  slideshow-component[actioned] & {
    content-visibility: visible;
  }
}

/*
 * Be specific about HTML children structure to avoid targeting nested slideshows.
 * Ensure that the content is 'visible' while scrolling instead of 'auto' to avoid issues in Safari.
 */
slideshow-component:is([dragging], [transitioning], :hover) > slideshow-container > slideshow-slides > slideshow-slide {
  content-visibility: visible;
}
```

### Documentation
- [MDN content-visibility](https://developer.mozilla.org/en-US/docs/Web/CSS/content-visibility)
- [Web.dev content-visibility Guide](https://web.dev/content-visibility/)

---

## 17. AbortController API

**Browser Support**: All modern browsers

### Implementation
Cancellable async operations and clean event listener management.

### Code Examples

```javascript
// assets/accordion-custom.js
class AccordionCustom extends HTMLElement {
  #controller = new AbortController();

  connectedCallback() {
    const { signal } = this.#controller;
    
    this.summary.addEventListener('click', this.handleClick, { signal });
    this.summary.addEventListener('keydown', this.handleKeydown, { signal });
    
    if (this.#closeWithEscape) {
      document.addEventListener('keydown', this.handleEscapeKey, { signal });
    }
  }

  disconnectedCallback() {
    this.#controller.abort();
  }

  handleClick = (event) => {
    const isMobile = isMobileBreakpoint();
    const isDesktop = !isMobile;

    if ((isMobile && this.#disableOnMobile) || (isDesktop && this.#disableOnDesktop)) {
      event.preventDefault();
      return;
    }
  };
}

// assets/slideshow.js - Complex event management
async connectedCallback() {
  super.connectedCallback();
  
  const { scroller } = this.refs;
  const controller = new AbortController();
  
  // Pointer event handling with cleanup
  const onPointerDown = (event) => {
    const pointerController = new AbortController();
    
    const onPointerMove = (event) => {
      // Handle drag
    };
    
    const onPointerUp = async (event) => {
      pointerController.abort();
      // Handle release
    };
    
    document.addEventListener('pointermove', onPointerMove, {
      signal: pointerController.signal
    });
    document.addEventListener('pointerup', onPointerUp, {
      signal: pointerController.signal
    });
  };
  
  scroller.addEventListener('pointerdown', onPointerDown, {
    signal: controller.signal
  });
  
  this.disconnectedCallback = () => {
    controller.abort();
  };
}
```

### Documentation
- [MDN AbortController](https://developer.mozilla.org/en-US/docs/Web/API/AbortController)
- [Fetch API Cancellation](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch#canceling_a_request)

---

## 18. CSS `:has()` Selector

**Browser Support**: Chrome 105+, Firefox 121+, Safari 15.4+

### Implementation
Parent selector and conditional styling capabilities.

### Code Examples

```css
/* assets/base.css */
/* Typography spacing with parent context */
:is(p, h1, h2, h3, h4, h5, h6):last-child,
:where(p, h1, h2, h3, h4, h5, h6) + :has(+ :empty:last-child) {
  margin-block-end: 0;
}

/* Media state handling */
.product-media-container:has(.deferred-media__playing) .product-media__image {
  opacity: 0;
  transition: opacity var(--animation-speed) var(--animation-easing);
}

/* Focus management */
deferred-media:has(:focus-visible) {
  outline: var(--focus-outline-width) solid currentcolor;
  outline-offset: var(--focus-outline-offset);
}

/* Slideshow performance optimization */
:is(.collection-list__carousel, .resource-list__carousel, .card-gallery)
  :is(
    slideshow-slide:has(+ slideshow-slide[aria-hidden='false']),
    slideshow-slide[aria-hidden='false'] + slideshow-slide
  ) {
  content-visibility: auto;
}

/* Container state styling */
.card-wrapper:has(.product-card:hover) {
  z-index: var(--layer-selected);
}

/* Form validation */
.form-field:has(input:invalid) .field-label {
  color: var(--color-error);
}

.form-field:has(input:focus) .field-label {
  color: var(--color-primary);
}

/* Conditional button display */
.product-form:has(.variant-picker[data-available='false']) .add-to-cart-button {
  opacity: 0.5;
  pointer-events: none;
}

/* Layout adjustments based on content */
.section:has(.section-header:empty) {
  padding-block-start: 0;
}

.card-gallery:has(.quick-add__button:hover) {
  overflow: visible;
}
```

### Documentation
- [MDN :has() selector](https://developer.mozilla.org/en-US/docs/Web/CSS/:has)
- [CSS Selectors Level 4](https://www.w3.org/TR/selectors-4/#relational)

---

## Browser Support Summary

| Feature | Chrome | Firefox | Safari | Edge |
|---------|---------|---------|---------|---------|
| View Transitions API | 111+ | ❌ (experimental) | ❌ (experimental) | 111+ |
| Container Queries | 105+ | 110+ | 16+ | 105+ |
| ES Modules | ✅ | ✅ | ✅ | ✅ |
| CSS Nesting | 112+ | 117+ | 16.5+ | 112+ |
| Custom Elements | ✅ | ✅ | ✅ | ✅ |
| `interpolate-size` | 129+ | ❌ | ❌ | 129+ |
| `@starting-style` | 117+ | ❌ | 17.4+ | 117+ |
| Relative Color Syntax | 111+ | 113+ | 16.4+ | 111+ |
| Intersection Observer | ✅ | ✅ | ✅ | ✅ |
| ResizeObserver | ✅ | ✅ | ✅ | ✅ |
| Logical Properties | ✅ | ✅ | ✅ | ✅ |
| Scroll Snap | ✅ | ✅ | ✅ | ✅ |
| CSS Variables | ✅ | ✅ | ✅ | ✅ |
| CSS Grid | ✅ | ✅ | ✅ | ✅ |
| AbortController | ✅ | ✅ | ✅ | ✅ |
| `:has()` Selector | 105+ | 121+ | 15.4+ | 105+ |
| `content-visibility` | 85+ | 125+ | ❌ | 85+ |

## Performance Considerations

### Progressive Enhancement
The theme implements progressive enhancement patterns:
- Core functionality works without modern features
- Enhanced experiences for supporting browsers
- Graceful degradation for older browsers

### Performance Optimizations
- `content-visibility` for rendering performance
- Intersection Observer for scroll performance
- ResizeObserver for layout performance
- View Transitions for perceived performance
- ES Modules for better caching and loading

### Accessibility
- `prefers-reduced-motion` support throughout
- Proper ARIA attributes and roles
- Keyboard navigation support
- Focus management with `:focus-visible`

## Development Guidelines

### Feature Detection
Always implement feature detection for experimental features:

```javascript
// View Transitions
if (typeof document.startViewTransition === 'function') {
  // Use View Transitions
} else {
  // Fallback behavior
}

// CSS support detection
if (CSS.supports('interpolate-size', 'allow-keywords')) {
  // Use interpolate-size
}
```

### Graceful Degradation
Ensure core functionality works without modern features:

```css
/* Base functionality */
.accordion-content {
  display: none;
}

.accordion[open] .accordion-content {
  display: block;
}

/* Enhanced with modern CSS */
@supports (interpolate-size: allow-keywords) {
  .accordion-content {
    display: block;
    height: 0;
    overflow: hidden;
    interpolate-size: allow-keywords;
    transition: height 0.3s ease;
  }
  
  .accordion[open] .accordion-content {
    height: auto;
  }
}
```

---

*This documentation reflects the state of the Shopify Horizon theme as of June 2025 and represents some of the most advanced web development techniques available in modern browsers.*
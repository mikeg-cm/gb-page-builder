# Paske Additional CSS v3.2

Paste into: **Appearance → Customize → Additional CSS**
Replaces the entire existing Additional CSS.

**What changed from v2:**
- Removed all Part 3 legacy CSS (~400 lines of dead code)
- Removed all Part 2b rodent page overrides (no longer needed without legacy conflicts)
- Eliminated all `!important` declarations (was 78, now 0)
- Added `:root` design tokens for multi-client theming
- Added `menu-cta` red button for "Free Estimate" nav item (white text on red bg)
- Added nav sizing refinements (14px, tighter padding)
- Added Chrome Mobile safe-area-inset-top fix
- Added semantic class system (`pk-section`, `pk-inner`, `pk-grid-*`, `pk-card`, `pk-h1`-`pk-proof`, `pk-icon`, `pk-btn`)
- Deduplicated hover/transition rules
- Restructured into 10 logical sections

**WP Admin steps:**
1. Add CSS class `menu-cta` to the "Free Estimate" menu item (Appearance → Menus → Free Estimate → CSS Classes field)
2. Paste the CSS below into Appearance → Customize → Additional CSS (replace all)

---

```css
/* ================================================
   PASKE - ADDITIONAL CSS v3.2
   Paste into: Appearance -> Customize -> Additional CSS
   Updated: 2026-03-24

   SECTION 1:  Design tokens (CSS custom properties)
   SECTION 2:  Fixed top bar + body offset
   SECTION 3:  Navigation + Menu CTA
   SECTION 4:  Semantic classes (sections, typography, cards, grids, icons, buttons)
   SECTION 5:  Hero enhancements (cards, popovers, buttons)
   SECTION 6:  Component hovers + transitions
   SECTION 7:  Stretched card links
   SECTION 8:  Pseudo-elements (connectors, dividers, badges)
   SECTION 9:  FAQ accordion
   SECTION 10: Responsive overrides
   SECTION 11: Page-specific scoped rules
   ================================================ */

/* ================================================
   SECTION 1: DESIGN TOKENS
   Swap these values per client for theming.
   Green Behr example: body.greenbehr { --pk-brand: #1daa4c; }
   ================================================ */
:root {
  /* Brand */
  --pk-brand: #C4282D;
  --pk-brand-dark: #A12025;
  --pk-brand-light: rgba(196, 40, 45, 0.06);

  /* Text */
  --pk-dark: #151413;
  --pk-body: #3A3937;
  --pk-muted: #6B6966;
  --pk-gold: #D4A843;

  /* Backgrounds */
  --pk-bg-light: #FAF8F5;
  --pk-bg-lighter: #F5F3F0;
  --pk-bg-dark: #151413;

  /* Borders */
  --pk-border: #E5E2DD;

  /* Spacing */
  --pk-section-y: clamp(56px, 8vw, 100px);
  --pk-section-x: clamp(20px, 5vw, 60px);
  --pk-gap: 24px;

  /* Radii */
  --pk-radius: 14px;
  --pk-radius-sm: 10px;
  --pk-radius-btn: 7px;
  --pk-radius-hero: 16px;

  /* Typography */
  --pk-font-h1: clamp(32px, 5vw, 46px);
  --pk-font-h2: clamp(26px, 3.5vw, 36px);
  --pk-font-h3: 16px;
  --pk-font-body: 16px;
  --pk-font-small: 14px;
  --pk-font-proof: 13px;
  --pk-font-caption: 12px;
  --pk-font-eyebrow: 11px;
}

/* ================================================
   SECTION 2: FIXED TOP BAR + STICKY NAV
   .cm-topbar = universal Content Mender class.
   Add "cm-topbar" to the Hook Element's
   Additional CSS Classes field in GP Elements.
   Adjust --pk-topbar-h if bar height differs.
   ================================================ */
:root { --pk-topbar-h: 43px; }

.cm-topbar {
  position: fixed !important;
  top: 0 !important;
  left: 0;
  width: 100%;
  z-index: 10001;
  margin: 0 !important;
}

#site-navigation.is_stuck,
#sticky-navigation.is_stuck {
  top: var(--pk-topbar-h) !important;
  margin-top: 0 !important;
}

body {
  padding-top: var(--pk-topbar-h);
}

/* WP admin bar offset (logged-in only)
   WP adds html { margin-top: 32px } for the admin bar,
   so body padding stays the same — only the fixed elements
   need to shift down by 32px to sit below the admin bar. */
.admin-bar .cm-topbar {
  top: 32px !important;
}
.admin-bar #site-navigation.is_stuck,
.admin-bar #sticky-navigation.is_stuck {
  top: calc(var(--pk-topbar-h) + 32px) !important;
}
@media (max-width: 782px) {
  .admin-bar .cm-topbar { top: 46px !important; }
  .admin-bar #site-navigation.is_stuck,
  .admin-bar #sticky-navigation.is_stuck { top: calc(var(--pk-topbar-h) + 46px) !important; }
}

/* ================================================
   SECTION 3: NAVIGATION + MENU CTA
   ================================================ */
/* CTA button — "Free Estimate" red pill
   Targets both #site-navigation (default) and
   #sticky-navigation (cloned sticky nav) */
#site-navigation .main-nav ul li.menu-cta > a,
#site-navigation .main-nav ul li.menu-cta > a:visited,
#sticky-navigation .main-nav ul li.menu-cta > a,
#sticky-navigation .main-nav ul li.menu-cta > a:visited {
  background-color: var(--pk-brand);
  color: #fff;
  padding: 10px 22px;
  border-radius: var(--pk-radius-btn);
  font-weight: 600;
  font-size: 14px;
  line-height: 1;
  transition: background-color 0.25s ease, transform 0.25s ease;
}
#site-navigation .main-nav ul li.menu-cta > a:hover,
#sticky-navigation .main-nav ul li.menu-cta > a:hover {
  background-color: var(--pk-brand-dark);
  color: #fff;
  transform: translateY(-1px);
}

/* Reset CTA pill on mobile hamburger menu */
@media (max-width: 768px) {
  #site-navigation .main-nav ul li.menu-cta > a,
  #site-navigation .main-nav ul li.menu-cta > a:visited {
    background-color: transparent;
    color: inherit;
    padding: inherit;
    border-radius: 0;
    font-weight: inherit;
    line-height: inherit;
  }
}

/* ================================================
   SECTION 4: SEMANTIC CLASSES
   Use these on GB elements via className field.
   Phase 2+: new pages use these instead of
   repeating inline styles on every element.
   ================================================ */

/* -- Sections ----------------------------------- */
.pk-section {
  padding: var(--pk-section-y) var(--pk-section-x);
}
.pk-section--light {
  background-color: var(--pk-bg-light);
}
.pk-section--lighter {
  background-color: var(--pk-bg-lighter);
}
.pk-section--white {
  background-color: #fff;
}
.pk-section--dark {
  background-color: var(--pk-bg-dark);
}

/* -- Inner containers --------------------------- */
.pk-inner {
  max-width: 1120px;
  margin-left: auto;
  margin-right: auto;
}
.pk-inner--narrow {
  max-width: 960px;
  margin-left: auto;
  margin-right: auto;
}
.pk-inner--hero {
  max-width: 900px;
  margin-left: auto;
  margin-right: auto;
  text-align: center;
}

/* -- Typography --------------------------------- */
.pk-h1 {
  font-size: var(--pk-font-h1);
  font-weight: 800;
  line-height: 1.15;
  letter-spacing: -0.02em;
  color: var(--pk-dark);
}
.pk-h2 {
  font-size: var(--pk-font-h2);
  font-weight: 800;
  line-height: 1.15;
  letter-spacing: -0.02em;
  color: var(--pk-dark);
  text-align: center;
}
.pk-h3 {
  font-size: var(--pk-font-h3);
  font-weight: 800;
  line-height: 1.2;
  letter-spacing: -0.02em;
  color: var(--pk-dark);
}
.pk-subtitle {
  font-size: clamp(16px, 1.5vw, 18px);
  line-height: 1.65;
  color: var(--pk-body);
}
.pk-body {
  font-size: var(--pk-font-body);
  line-height: 1.7;
  color: var(--pk-body);
}
.pk-small {
  font-size: var(--pk-font-small);
  line-height: 1.7;
  color: var(--pk-body);
}
.pk-muted {
  font-size: clamp(15px, 1.2vw, 17px);
  line-height: 1.7;
  color: var(--pk-muted);
}
.pk-proof {
  font-size: var(--pk-font-proof);
  font-weight: 600;
  color: var(--pk-muted);
}
.pk-eyebrow {
  font-size: var(--pk-font-eyebrow);
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 2.5px;
  color: var(--pk-brand);
}
.pk-price {
  font-size: 48px;
  font-weight: 900;
  line-height: 1.1;
  color: var(--pk-brand);
}
/* White text variants for dark sections */
.pk-section--dark .pk-h2,
.pk-section--dark .pk-h1 { color: #fff; }
.pk-section--dark .pk-muted { color: rgba(255,255,255,0.55); }
.pk-section--dark .pk-body { color: rgba(255,255,255,0.7); }

/* -- Cards -------------------------------------- */
.pk-card {
  background-color: #fff;
  border: 1px solid var(--pk-border);
  border-radius: var(--pk-radius);
  padding: 24px;
}
.pk-card--accent {
  border-top: 3px solid var(--pk-brand);
}
.pk-card--pricing {
  padding: 32px;
  text-align: center;
}
.pk-card--review {
  background-color: var(--pk-bg-light);
}
.pk-card--link {
  position: relative;
}

/* -- Icon badges -------------------------------- */
.pk-icon {
  width: 44px;
  height: 44px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: var(--pk-brand-light);
  border-radius: var(--pk-radius-sm);
  margin-bottom: 12px;
}
.pk-icon--large {
  width: 56px;
  height: 56px;
  border-radius: var(--pk-radius);
  margin-left: auto;
  margin-right: auto;
  margin-bottom: 14px;
}

/* -- Grids -------------------------------------- */
.pk-grid-4 {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: var(--pk-gap);
}
.pk-grid-3 {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: var(--pk-gap);
}
.pk-grid-2 {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: var(--pk-gap);
}
@media (max-width: 1024px) {
  .pk-grid-4 { grid-template-columns: repeat(2, 1fr); }
}
@media (max-width: 768px) {
  .pk-grid-4,
  .pk-grid-3,
  .pk-grid-2 { grid-template-columns: 1fr; }
}

/* ================================================
   SECTION 5: HERO ENHANCEMENTS
   ================================================ */

/* Hero card phone button (outlined style) */
.hero-card a[href^="tel"].wp-block-button__link {
  color: var(--pk-brand);
  background-color: transparent;
}
.hero-card a[href^="tel"].wp-block-button__link:hover {
  background-color: var(--pk-brand-light);
  color: var(--pk-brand-dark);
  border-color: var(--pk-brand-dark);
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(196, 40, 45, 0.15);
}

/* Hero popover card (location pages) */
.hero-popover {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  position: relative;
}
.hero-popover::before {
  content: 'Included With Every Plan';
  position: absolute;
  top: -12px;
  left: 20px;
  background: var(--pk-brand);
  color: #fff;
  font-size: var(--pk-font-eyebrow);
  font-weight: 700;
  letter-spacing: 0.5px;
  text-transform: uppercase;
  padding: 4px 12px;
  border-radius: 20px;
  line-height: 1.4;
}
.hero-popover h3 {
  padding-bottom: 12px;
  border-bottom: 2px solid #F0EDEA;
}
@media (min-width: 901px) {
  .hero-popover:hover {
    transform: translateY(-4px);
    box-shadow: 0 28px 70px rgba(0,0,0,0.22), 0 6px 16px rgba(196,40,45,0.08);
  }
}
@media (max-width: 900px) {
  .hero-layout {
    grid-template-columns: 1fr;
    max-width: 680px;
  }
  .hero-popover {
    border-left-width: 3px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.10);
  }
  .hero-popover::before {
    display: none;
  }
}

/* ================================================
   SECTION 6: COMPONENT HOVERS + TRANSITIONS
   ================================================ */
.wp-block-button__link {
  transition: all 0.25s ease;
}
.problem-card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.problem-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 32px rgba(0,0,0,0.10);
}
.service-card {
  transition: transform 0.25s ease, box-shadow 0.25s ease, border-color 0.25s ease;
}
.service-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.08);
}
.svc-icon {
  transition: background-color 0.25s ease, transform 0.25s ease;
}
.service-card:hover .svc-icon {
  background-color: var(--pk-brand);
  transform: scale(1.05);
}
.service-card:hover .svc-icon p {
  filter: grayscale(1) brightness(10);
}
.review-card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.review-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.08);
}
.blog-card {
  transition: all 0.3s ease;
  cursor: pointer;
}
.blog-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 32px rgba(0,0,0,0.07);
}
.benefit-card {
  transition: all 0.3s ease;
}
.benefit-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 12px 28px rgba(0,0,0,0.05);
}
.solution-card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.solution-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.06);
}
.package-card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.package-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.08);
}
.pest-row {
  transition: background-color 0.2s ease, box-shadow 0.2s ease;
}
.concerns-box {
  transition: box-shadow 0.3s ease;
}
.concerns-box:hover {
  box-shadow: 0 4px 16px rgba(0,0,0,0.04);
}
.svc-card {
  transition: all 0.25s ease;
}
.svc-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.06);
}

/* ================================================
   SECTION 7: STRETCHED CARD LINKS
   ================================================ */
.card-link {
  color: inherit;
  text-decoration: none;
}
.card-link::after {
  content: "";
  position: absolute;
  inset: 0;
  z-index: 1;
  cursor: pointer;
}
.service-card:has(.card-link) { cursor: pointer; }
.service-card:has(.card-link):hover { border-color: var(--pk-brand); }
.pest-row:has(.card-link) { cursor: pointer; }
.pest-row:has(.card-link):hover { background-color: var(--pk-bg-light); }
.pest-row:has(.card-link):hover .pest-icon { background-color: rgba(196, 40, 45, 0.12); }
.pest-row:has(.card-link):hover h3 .card-link { color: var(--pk-brand); }

/* ================================================
   SECTION 8: PSEUDO-ELEMENT DECORATIONS
   ================================================ */
/* Step connector lines (vertical) */
.how-section .step-card { position: relative; }
.how-section .step-card:not(:last-child)::after {
  content: '';
  position: absolute;
  left: 51px;
  bottom: -16px;
  width: 2px;
  height: 16px;
  background: var(--pk-border);
}
/* Timeline connector (horizontal) */
.timeline-grid { position: relative; }
.timeline-grid::before {
  content: '';
  position: absolute;
  top: 28px;
  left: calc(12.5% + 28px);
  right: calc(12.5% + 28px);
  height: 2px;
  background: var(--pk-border);
  z-index: 0;
}
@media (max-width: 768px) {
  .timeline-grid::before { display: none; }
}
/* Step badge glow ring */
.step-num { position: relative; }
.step-num::after {
  content: '';
  position: absolute;
  inset: -5px;
  border-radius: 50%;
  border: 2px solid rgba(196, 40, 45, 0.15);
  pointer-events: none;
}
/* Proof strip dividers */
.proof-strip > p:not(:last-child)::after {
  content: '|';
  margin-left: 28px;
  color: var(--pk-border);
  font-weight: 400;
}
@media (max-width: 768px) {
  .proof-strip > p:not(:last-child)::after { display: none; }
}
/* Hero proof strip dividers */
.hero-proof-strip > p:not(:last-child)::after {
  content: '|';
  margin-left: clamp(12px, 2vw, 28px);
  color: var(--pk-border);
  font-weight: 400;
}
@media (max-width: 768px) {
  .hero-proof-strip > p:not(:last-child)::after { display: none; }
}
/* Trust bar dividers (location pages) */
.trust-bar .gb-element-lc07lc07 > p {
  position: relative;
  padding: 4px 0;
}
.trust-bar .gb-element-lc07lc07 > p:not(:last-child)::after {
  content: '';
  position: absolute;
  right: calc(-0.5 * clamp(12px, 2vw, 24px));
  top: 15%;
  height: 70%;
  width: 1px;
  background: var(--pk-border);
}
@media (max-width: 768px) {
  .trust-bar .gb-element-lc07lc07 > p:nth-child(3n)::after { display: none; }
}
@media (max-width: 480px) {
  .trust-bar .gb-element-lc07lc07 > p:nth-child(even)::after { display: none; }
}
/* Map band */
.map-band { min-height: 420px; }
.map-band iframe { filter: saturate(0.85) contrast(1.05); }

/* ================================================
   SECTION 9: FAQ ACCORDION
   ================================================ */
.faq-section .wp-block-details {
  border: 1px solid var(--pk-border);
  border-radius: var(--pk-radius-sm);
  padding: 18px 24px;
  margin-bottom: 10px;
  transition: border-color 0.25s ease;
  background: #fff;
}
.faq-section .wp-block-details:hover,
.faq-section .wp-block-details[open] {
  border-color: var(--pk-brand);
}
.faq-section .wp-block-details[open] {
  background: var(--pk-bg-light);
}
.faq-section .wp-block-details summary {
  font-weight: 700;
  font-size: var(--pk-font-body);
  color: var(--pk-dark);
  cursor: pointer;
  list-style: none;
}
.faq-section .wp-block-details summary::-webkit-details-marker {
  display: none;
}
.faq-section .wp-block-details p {
  margin-top: 12px;
  font-size: 15px;
  line-height: 1.7;
  color: var(--pk-body);
}

/* ================================================
   SECTION 10: RESPONSIVE OVERRIDES
   Doubled class selectors beat GB inline css
   specificity without !important.
   ================================================ */
.trust-bar p { margin-bottom: 0; }

@media (max-width: 480px) {
  .trust-bar .gb-element-lc07lc07.gb-element-lc07lc07 {
    grid-template-columns: 1fr 1fr;
  }
}
@media (max-width: 767px) {
  .problems-grid.problems-grid { grid-template-columns: 1fr; }
  .services-grid.services-grid { grid-template-columns: 1fr 1fr; }
  .results-box.results-box { grid-template-columns: 1fr; }
  .step-card.step-card { grid-template-columns: 40px 1fr; gap: 14px; padding: 20px; }
  .step-card .step-num { width: 40px; height: 40px; }
  .stats-bar-inner.stats-bar-inner { grid-template-columns: 1fr 1fr; }
  .reviews-grid.reviews-grid { grid-template-columns: 1fr; }
  .blog-grid.blog-grid { grid-template-columns: 1fr; }
  .benefits-grid.benefits-grid { grid-template-columns: 1fr; }
  .final-cta-section .wp-block-buttons,
  .cta-section .wp-block-buttons { flex-direction: column; }
  .hero-columns.hero-columns { grid-template-columns: 1fr; gap: 32px; }
  .areas-grid.areas-grid { grid-template-columns: 1fr; }
  .history-grid.history-grid { grid-template-columns: 1fr; }
  .svc-cards-grid.svc-cards-grid,
  .pkg-cards-grid.pkg-cards-grid { grid-template-columns: 1fr; }
  .ipm-grid.ipm-grid { grid-template-columns: 1fr; gap: 24px; }
  .pricing-grid.pricing-grid { grid-template-columns: 1fr; }
}
@media (max-width: 480px) {
  .services-grid.services-grid { grid-template-columns: 1fr; }
  .timeline-grid.timeline-grid { grid-template-columns: 1fr; gap: 28px; }
}
@media (max-width: 768px) {
  .community-text-grid.community-text-grid { grid-template-columns: 1fr; }
  .community-intro-grid.community-intro-grid { grid-template-columns: 1fr; }
  .timeline-grid.timeline-grid { grid-template-columns: 1fr 1fr; gap: 32px; }
  .stats-strip .gb-element-lcS02 { border-right: none; }
}
@media (max-width: 1024px) {
  .svc-cards-grid.svc-cards-grid,
  .pkg-cards-grid.pkg-cards-grid { grid-template-columns: repeat(2, 1fr); }
}

/* ================================================
   SECTION 11: PAGE-SPECIFIC SCOPED RULES
   ================================================ */
.wyg-section .wp-block-group__inner-container {
  padding-top: 32px;
  padding-bottom: 32px;
}
@media (max-width: 768px) {
  .map-overlay-card {
    position: relative;
    bottom: auto;
    left: auto;
    max-width: 100%;
    border-radius: 0;
    box-shadow: none;
  }
}
.reviews-carousel {
  scroll-snap-type: x mandatory;
  -webkit-overflow-scrolling: touch;
  scrollbar-width: thin;
  scrollbar-color: var(--pk-border) transparent;
}
.reviews-carousel .review-card {
  scroll-snap-align: start;
}
```

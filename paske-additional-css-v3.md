# Paske Additional CSS v3.0

Paste into: **Appearance → Customize → Additional CSS**
Replaces the entire existing Additional CSS.

**What changed from v2:**
- Removed all Part 3 legacy CSS (~400 lines of dead code)
- Removed all Part 2b rodent page overrides (no longer needed without legacy conflicts)
- Eliminated all `!important` declarations (was 78, now 0)
- Added menu CTA button (`.menu-cta`)
- Added nav sizing refinements
- Added Chrome Mobile top bar fix
- Deduplicated hover/transition rules
- Restructured into 9 logical sections for multi-client reuse

**WP Admin step:** Add CSS class `menu-cta` to the "Free Estimate" menu item (Appearance → Menus → Free Estimate → CSS Classes field)

---

```css
/* ================================================
   PASKE - ADDITIONAL CSS v3.0
   Paste into: Appearance -> Customize -> Additional CSS
   Updated: 2026-03-24

   SECTION 1: Fixed top bar + body offset
   SECTION 2: Navigation + Menu CTA
   SECTION 3: Hero enhancements (cards, popovers, buttons)
   SECTION 4: Component hovers + transitions
   SECTION 5: Stretched card links
   SECTION 6: Pseudo-elements (connectors, dividers, badges)
   SECTION 7: FAQ accordion
   SECTION 8: Responsive overrides
   SECTION 9: Page-specific scoped rules
   ================================================ */

/* ================================================
   SECTION 1: FIXED TOP BAR + BODY OFFSET
   The GB element ff8c003b is a fixed announcement bar.
   Body needs padding to prevent content hiding under it.
   ================================================ */
.gb-element-ff8c003b {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  z-index: 10001;
  margin: 0;
}
nav.navigation-clone.is_stuck,
nav#mobile-header.navigation-stick {
  top: 43px;
  margin-top: 0;
}
body {
  padding-top: 43px;
  padding-top: calc(43px + env(safe-area-inset-top, 0px));
}

/* ================================================
   SECTION 2: NAVIGATION + MENU CTA
   Tighter nav sizing and red CTA button for
   "Free Estimate" menu item (class: menu-cta)
   ================================================ */
/* Tighter nav items */
.main-navigation .main-nav > ul > li > a {
  font-size: 14px;
  padding: 12px 16px;
  font-weight: 500;
}

/* CTA button in nav */
.main-navigation .menu-cta > a {
  background-color: #C4282D;
  color: #ffffff;
  padding: 10px 22px;
  border-radius: 7px;
  font-weight: 600;
  font-size: 14px;
  transition: background-color 0.25s ease, transform 0.25s ease;
}
.main-navigation .menu-cta > a:hover {
  background-color: #A12025;
  transform: translateY(-1px);
  color: #ffffff;
}

/* Hide CTA button styling on mobile hamburger menu */
@media (max-width: 768px) {
  .main-navigation .menu-cta > a {
    background-color: transparent;
    color: inherit;
    padding: inherit;
    border-radius: 0;
    font-weight: inherit;
  }
}

/* ================================================
   SECTION 3: HERO ENHANCEMENTS
   Cover block hero cards, popovers, button fixes
   ================================================ */

/* -- Hero card phone button (outlined style) ---- */
.hero-card a[href^="tel"].wp-block-button__link {
  color: #C4282D;
  background-color: transparent;
}
.hero-card a[href^="tel"].wp-block-button__link:hover {
  background-color: rgba(196, 40, 45, 0.06);
  color: #A12025;
  border-color: #A12025;
  transform: translateY(-2px);
  box-shadow: 0 8px 24px rgba(196, 40, 45, 0.15);
}

/* -- Hero popover card (location pages) --------- */
.hero-popover {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  position: relative;
}
.hero-popover::before {
  content: 'Included With Every Plan';
  position: absolute;
  top: -12px;
  left: 20px;
  background: #C4282D;
  color: #ffffff;
  font-size: 11px;
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
   SECTION 4: COMPONENT HOVERS + TRANSITIONS
   Enhancement CSS for card interactions.
   GB inline styles handle base appearance;
   these add hover lift + shadow effects.
   ================================================ */

/* Global button transition */
.wp-block-button__link {
  transition: all 0.25s ease;
}

/* Problem cards */
.problem-card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.problem-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 32px rgba(0,0,0,0.10);
}

/* Service cards */
.service-card {
  transition: transform 0.25s ease, box-shadow 0.25s ease, border-color 0.25s ease;
}
.service-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.08);
}

/* Service card icon animation on hover */
.svc-icon {
  transition: background-color 0.25s ease, transform 0.25s ease;
}
.service-card:hover .svc-icon {
  background-color: #C4282D;
  transform: scale(1.05);
}
.service-card:hover .svc-icon p {
  filter: grayscale(1) brightness(10);
}

/* Review cards */
.review-card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.review-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.08);
}

/* Blog cards */
.blog-card {
  transition: all 0.3s ease;
  cursor: pointer;
}
.blog-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 32px rgba(0,0,0,0.07);
}

/* Benefit cards */
.benefit-card {
  transition: all 0.3s ease;
}
.benefit-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 12px 28px rgba(0,0,0,0.05);
}

/* Solution cards (package pages) */
.solution-card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.solution-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.06);
}

/* Package/pricing cards */
.package-card {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.package-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.08);
}

/* Pest rows */
.pest-row {
  transition: background-color 0.2s ease, box-shadow 0.2s ease;
}

/* Concerns box */
.concerns-box {
  transition: box-shadow 0.3s ease;
}
.concerns-box:hover {
  box-shadow: 0 4px 16px rgba(0,0,0,0.04);
}

/* Svc cards (services page grid) */
.svc-card {
  transition: all 0.25s ease;
}
.svc-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 24px rgba(0,0,0,0.06);
}

/* ================================================
   SECTION 5: STRETCHED CARD LINKS
   Cards with .card-link get full-card click area
   via ::after pseudo-element overlay.
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

/* Service cards with links: hover accent */
.service-card:has(.card-link) {
  cursor: pointer;
}
.service-card:has(.card-link):hover {
  border-color: #C4282D;
}

/* Pest rows with links: hover highlight */
.pest-row:has(.card-link) {
  cursor: pointer;
}
.pest-row:has(.card-link):hover {
  background-color: #FAF8F5;
}
.pest-row:has(.card-link):hover .pest-icon {
  background-color: rgba(196, 40, 45, 0.12);
}
.pest-row:has(.card-link):hover h3 .card-link {
  color: #C4282D;
}

/* ================================================
   SECTION 6: PSEUDO-ELEMENT DECORATIONS
   Connector lines, dividers, badges, glow rings
   ================================================ */

/* -- Step connector lines (how-it-works vertical) */
.how-section .step-card {
  position: relative;
}
.how-section .step-card:not(:last-child)::after {
  content: '';
  position: absolute;
  left: 51px;
  bottom: -16px;
  width: 2px;
  height: 16px;
  background: #E5E2DD;
}

/* -- Timeline connector (horizontal process steps) */
.timeline-grid {
  position: relative;
}
.timeline-grid::before {
  content: '';
  position: absolute;
  top: 28px;
  left: calc(12.5% + 28px);
  right: calc(12.5% + 28px);
  height: 2px;
  background: #E5E2DD;
  z-index: 0;
}
@media (max-width: 768px) {
  .timeline-grid::before {
    display: none;
  }
}

/* -- Step badge glow ring ----------------------- */
.step-num {
  position: relative;
}
.step-num::after {
  content: '';
  position: absolute;
  inset: -5px;
  border-radius: 50%;
  border: 2px solid rgba(196, 40, 45, 0.15);
  pointer-events: none;
}

/* -- Proof strip dividers ----------------------- */
.proof-strip > p:not(:last-child)::after {
  content: '|';
  margin-left: 28px;
  color: #E5E2DD;
  font-weight: 400;
}
@media (max-width: 768px) {
  .proof-strip > p:not(:last-child)::after {
    display: none;
  }
}

/* -- Hero proof strip (inside card) ------------- */
.hero-proof-strip > p:not(:last-child)::after {
  content: '|';
  margin-left: clamp(12px, 2vw, 28px);
  color: #E5E2DD;
  font-weight: 400;
}
@media (max-width: 768px) {
  .hero-proof-strip > p:not(:last-child)::after {
    display: none;
  }
}

/* -- Trust bar dividers (location pages) -------- */
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
  background: #E5E2DD;
}
@media (max-width: 768px) {
  .trust-bar .gb-element-lc07lc07 > p:nth-child(3n)::after {
    display: none;
  }
}
@media (max-width: 480px) {
  .trust-bar .gb-element-lc07lc07 > p:nth-child(even)::after {
    display: none;
  }
}

/* -- Map band ----------------------------------- */
.map-band {
  min-height: 420px;
}
.map-band iframe {
  filter: saturate(0.85) contrast(1.05);
}

/* ================================================
   SECTION 7: FAQ ACCORDION
   Styles for wp:details blocks inside .faq-section
   ================================================ */
.faq-section .wp-block-details {
  border: 1px solid #E5E2DD;
  border-radius: 10px;
  padding: 18px 24px;
  margin-bottom: 10px;
  transition: border-color 0.25s ease;
  background: #ffffff;
}
.faq-section .wp-block-details:hover,
.faq-section .wp-block-details[open] {
  border-color: #C4282D;
}
.faq-section .wp-block-details[open] {
  background: #FAF8F5;
}
.faq-section .wp-block-details summary {
  font-weight: 700;
  font-size: 16px;
  color: #151413;
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
  color: #3A3937;
}

/* ================================================
   SECTION 8: RESPONSIVE OVERRIDES
   Mobile breakpoints for GB grids. These override
   the inline css strings from GB elements.
   Specificity: doubled class selector beats the
   single .gb-element-{id} class in inline css.
   ================================================ */

/* -- Trust bar ---------------------------------- */
.trust-bar p {
  margin-bottom: 0;
}
@media (max-width: 480px) {
  .trust-bar .gb-element-lc07lc07.gb-element-lc07lc07 {
    grid-template-columns: 1fr 1fr;
  }
}

/* -- Problems grid ------------------------------ */
@media (max-width: 767px) {
  .problems-grid.problems-grid {
    grid-template-columns: 1fr;
  }
}

/* -- Services grid ------------------------------ */
@media (max-width: 767px) {
  .services-grid.services-grid {
    grid-template-columns: 1fr 1fr;
  }
  .results-box.results-box {
    grid-template-columns: 1fr;
  }
}
@media (max-width: 480px) {
  .services-grid.services-grid {
    grid-template-columns: 1fr;
  }
}

/* -- How It Works (vertical step cards) --------- */
@media (max-width: 767px) {
  .step-card.step-card {
    grid-template-columns: 40px 1fr;
    gap: 14px;
    padding: 20px;
  }
  .step-card .step-num {
    width: 40px;
    height: 40px;
  }
}

/* -- Stats bar ---------------------------------- */
@media (max-width: 767px) {
  .stats-bar-inner.stats-bar-inner {
    grid-template-columns: 1fr 1fr;
  }
}
@media (max-width: 768px) {
  .stats-strip .gb-element-lcS02 {
    border-right: none;
  }
}

/* -- Reviews grid ------------------------------- */
@media (max-width: 767px) {
  .reviews-grid.reviews-grid {
    grid-template-columns: 1fr;
  }
}

/* -- Blog grid ---------------------------------- */
@media (max-width: 767px) {
  .blog-grid.blog-grid {
    grid-template-columns: 1fr;
  }
}

/* -- Benefits grid ------------------------------ */
@media (max-width: 767px) {
  .benefits-grid.benefits-grid {
    grid-template-columns: 1fr;
  }
}

/* -- Final CTA buttons stack on mobile ---------- */
@media (max-width: 767px) {
  .final-cta-section .wp-block-buttons,
  .cta-section .wp-block-buttons {
    flex-direction: column;
  }
}

/* -- Location page grids ------------------------ */
@media (max-width: 767px) {
  .hero-columns.hero-columns {
    grid-template-columns: 1fr;
    gap: 32px;
  }
  .areas-grid.areas-grid {
    grid-template-columns: 1fr;
  }
  .history-grid.history-grid {
    grid-template-columns: 1fr;
  }
}
@media (max-width: 768px) {
  .community-text-grid.community-text-grid {
    grid-template-columns: 1fr;
  }
  .community-intro-grid.community-intro-grid {
    grid-template-columns: 1fr;
  }
}

/* -- Timeline grid (horizontal process) --------- */
@media (max-width: 768px) {
  .timeline-grid.timeline-grid {
    grid-template-columns: 1fr 1fr;
    gap: 32px;
  }
}
@media (max-width: 480px) {
  .timeline-grid.timeline-grid {
    grid-template-columns: 1fr;
    gap: 28px;
  }
}

/* -- Services page card grids ------------------- */
@media (max-width: 1024px) {
  .svc-cards-grid.svc-cards-grid,
  .pkg-cards-grid.pkg-cards-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}
@media (max-width: 767px) {
  .svc-cards-grid.svc-cards-grid,
  .pkg-cards-grid.pkg-cards-grid {
    grid-template-columns: 1fr;
  }
  .ipm-grid.ipm-grid {
    grid-template-columns: 1fr;
    gap: 24px;
  }
}

/* -- Pricing grid (package pages) --------------- */
@media (max-width: 768px) {
  .pricing-grid.pricing-grid {
    grid-template-columns: 1fr;
  }
}

/* ================================================
   SECTION 9: PAGE-SPECIFIC SCOPED RULES
   Rules that only apply to specific pages.
   Use parent section class for scoping.
   ================================================ */

/* -- WYG section padding fix -------------------- */
.wyg-section .wp-block-group__inner-container {
  padding-top: 32px;
  padding-bottom: 32px;
}

/* -- Map overlay card mobile -------------------- */
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

/* -- Review carousel scroll-snap (rodent page) -- */
.reviews-carousel {
  scroll-snap-type: x mandatory;
  -webkit-overflow-scrolling: touch;
  scrollbar-width: thin;
  scrollbar-color: #E5E2DD transparent;
}
.reviews-carousel .review-card {
  scroll-snap-align: start;
}
```

# Dual Print Layout — Design Spec

## Overview

The existing "🖨️ Tulosta / PDF" button currently prints one set of cards (4 per page, 2×2 grid). This spec replaces that with a single print job containing two sections: an ingredients reference sheet and a clean display sheet.

## Print Job Structure

One button → one print job → two sections in order:

1. **Ingredients cards** — name + full ingredient list
2. **Display cards** — name + tagline + meat emojis

## Section 1: Ingredients Cards

Purpose: ingredient reference for allergy/dietary queries.

**Layout:**
- Single column, full page width (190mm)
- Cards stacked flush — no gap, no border radius
- Cards auto-size equally to fill the A4 page height (277mm minus 20mm margins)
- Up to 6 cards per page; overflow to next page if more selected
- Hairline separator between cards: `1px solid #e8e8e8`

**Card structure:**
- **Header** (colored, full width): product name only — small caps, bold, white, slight letter-spacing
- **Body** (white): full ingredient list — small font, generous line-height for readability

**Omitted:** tagline, flavor, meat tags, category badge, size, allergy badges, logo

## Section 2: Display Cards

Purpose: customer-facing display card placed at the sales counter to identify which meats/fish have been marinated with that marinade. The marinades are not sold standalone — the cards label the marinated products on display.

**Layout:**
- Single column, full page width (190mm)
- Cards stacked flush — no gap, no border radius
- Cards auto-size equally to fill the A4 page height
- Up to 6 cards per page; overflow to next page
- Hairline separator between cards: `1px solid rgba(255,255,255,.05)`

**Card structure:**
- **Top stripe**: 5px, product color
- **Body** (dark `#181818` background):
  - Name: small caps, bold, full white, 28pt, `letter-spacing: .06em` — large enough to read from across the counter
  - Tagline: italic, 13pt, `rgba(255,255,255,.45)`
- **Footer strip** (same dark background, separated by `1px solid rgba(255,255,255,.08)`):
  - Meat emojis: 20pt, `filter: grayscale(1) brightness(1.8); opacity: .7`
  - Allergy badges (Laktoositon, Gluteeniton): 9pt, subtle pill style, `rgba(255,255,255,.12)` background

**Omitted:** category badge, flavor text, ingredient list, size label, kapteeninkastikkeet.fi logo

## Auto-sizing Algorithm

Both sections use the same logic:

```
cardsOnPage = min(totalCards, 6)
cardHeight = pageHeight / cardsOnPage
```

If `totalCards > 6`, group into pages of 6 and repeat. Each page's cards divide the height equally via `grid-template-rows: repeat(N, 1fr)` where N = cards on that page.

## Print Color Fix

Background colors are stripped by default in most browsers. The `@media print` block includes:

```css
* { -webkit-print-color-adjust: exact; print-color-adjust: exact; }
```

This forces colored headers and the dark card backgrounds to render correctly in PDF/print output.

**Note:** Browser-generated headers/footers (URL, date, page number) cannot be removed via CSS. Users must uncheck "Headers and footers" in the browser print dialog.

## Implementation Notes

- Both `buildIngredientCards()` and `buildDisplayCards()` are separate functions called from `generatePDF()`
- Print area receives ingredient pages first, display pages after
- CSS classes: `icard` / `icard-header` / `icard-body` for ingredients; `scard` / `scard-stripe` / `scard-body` / `scard-footer` for display
- `@media print` hides header/main, shows `#print-area`
- Existing `pcard` styles can be removed (replaced entirely)
- Page break: `break-after: page` on each `.print-page` wrapper except last

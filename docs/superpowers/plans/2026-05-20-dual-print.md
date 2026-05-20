# Dual Print Layout Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace the current 2×2 print layout with a single print job containing two sections: an ingredients reference sheet and a dark display card sheet, both in single-column auto-sizing layout.

**Architecture:** All changes are in `index.html` — one HTML file with inline CSS and JS. The old `buildPrintCards()` function and all `pcard` CSS classes are removed and replaced with two builder functions (`buildIngredientCards`, `buildDisplayCards`) and two sets of CSS classes (`icard`, `scard`). Both sections share a common `.print-page` wrapper that uses a CSS custom property to set row count for auto-sizing.

**Tech Stack:** Vanilla HTML/CSS/JS. No build step. Open directly in browser. Print via `window.print()`.

---

### Task 1: Remove old print CSS and JS

**Files:**
- Modify: `index.html` — remove `pcard` CSS block and `buildPrintCards` function

- [ ] **Step 1: Remove all pcard CSS**

In `index.html`, find and delete the entire block of CSS rules that start with `.pcard` (lines ~152–188). These are the classes: `.pcard`, `.pcard-header`, `.pcard-cat`, `.pcard-name`, `.pcard-tagline`, `.pcard-body`, `.pcard-row`, `.pcard-label`, `.pcard-value`, `.pcard-ingredients`, `.pcard-meats`, `.pcard-meat`, `.pcard-footer`, `.pcard-badge`, `.pcard-size`, `.pcard-logo`.

Also remove the existing `@media print` block (lines ~134–150) and the `.print-page` rule inside it. We will replace these in Task 2.

- [ ] **Step 2: Remove buildPrintCards function**

In `index.html`, find and delete the entire `buildPrintCards()` function (lines ~601–640).

- [ ] **Step 3: Verify the page still loads**

Open `http://localhost:7700` in a browser. The product grid should still display normally. Only the print path is broken at this point — that's expected.

- [ ] **Step 4: Commit**

```bash
cd /home/lappemikb/projects/marinade-generator
git add index.html
git commit -m "remove old pcard print styles and buildPrintCards function"
```

---

### Task 2: Add print page layout CSS and ingredient card CSS

**Files:**
- Modify: `index.html` — add new `@media print` block and `icard` styles

- [ ] **Step 1: Add the following CSS** inside the `<style>` tag, after the existing `.no-results` rule. Note: the `#print-area { display: none; }` rule already exists in the file — do not add it again, only add the `@media print` block and `icard` styles below.

```css
/* ── print shared ── */
@media print {
  header, main, .no-results { display: none !important; }
  #print-area { display: block !important; }
  @page { size: A4; margin: 10mm; }

  .print-page {
    display: grid;
    grid-template-rows: repeat(var(--card-count), 1fr);
    width: 190mm;
    height: 277mm;
    break-after: page;
  }
  .print-page:last-child { break-after: auto; }
}

/* ── ingredient cards ── */
.icard {
  display: flex;
  flex-direction: column;
  overflow: hidden;
  border-top: 1px solid #e8e8e8;
  min-height: 0;
}
.icard:first-child { border-top: none; }

.icard-header {
  padding: 4mm 8mm 3mm;
  color: #fff;
  flex-shrink: 0;
}
.icard-name {
  font-size: 13pt;
  font-weight: 800;
  font-variant: small-caps;
  letter-spacing: .04em;
  line-height: 1.2;
  font-family: 'Segoe UI', system-ui, sans-serif;
}
.icard-body {
  padding: 3mm 8mm 4mm;
  flex: 1;
  display: flex;
  align-items: center;
}
.icard-ingredients {
  font-size: 8pt;
  color: #444;
  line-height: 1.6;
  font-family: 'Segoe UI', system-ui, sans-serif;
}
```

- [ ] **Step 2: Verify CSS is valid**

Open `http://localhost:7700`. The page should load without errors. Open browser DevTools → Console, confirm no CSS parse errors.

- [ ] **Step 3: Commit**

```bash
cd /home/lappemikb/projects/marinade-generator
git add index.html
git commit -m "add print page layout and ingredient card CSS"
```

---

### Task 3: Add display card CSS

**Files:**
- Modify: `index.html` — add `scard` styles

- [ ] **Step 1: Add the following CSS** inside the `<style>` tag, directly after the `.icard-ingredients` rule from Task 2:

```css
/* ── display cards ── */
.scard {
  display: flex;
  flex-direction: column;
  background: #181818;
  overflow: hidden;
  border-top: 1px solid rgba(255,255,255,.05);
  min-height: 0;
}
.scard:first-child { border-top: none; }

.scard-stripe {
  height: 3px;
  flex-shrink: 0;
}
.scard-body {
  padding: 4mm 8mm 3mm;
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.scard-name {
  font-size: 13pt;
  font-weight: 800;
  color: #ffffff;
  font-variant: small-caps;
  letter-spacing: .06em;
  line-height: 1.2;
  margin-bottom: 1.5mm;
  font-family: 'Segoe UI', system-ui, sans-serif;
}
.scard-tagline {
  font-size: 8pt;
  color: rgba(255,255,255,.45);
  font-style: italic;
  line-height: 1.3;
  font-family: 'Segoe UI', system-ui, sans-serif;
}
.scard-footer {
  padding: 2mm 8mm 3mm;
  border-top: 1px solid rgba(255,255,255,.08);
  display: flex;
  align-items: center;
  gap: 3mm;
  flex-shrink: 0;
}
.scard-emoji {
  font-size: 11pt;
  filter: grayscale(1) brightness(1.8);
  opacity: .7;
}
.scard-badges {
  margin-left: auto;
  display: flex;
  gap: 2mm;
}
.scard-badge {
  font-size: 6.5pt;
  font-weight: 700;
  padding: 0.8mm 2mm;
  border-radius: 2px;
  background: rgba(255,255,255,.12);
  color: rgba(255,255,255,.6);
  font-family: 'Segoe UI', system-ui, sans-serif;
}
```

- [ ] **Step 2: Verify no console errors**

Reload `http://localhost:7700`. Check DevTools console — should be clean.

- [ ] **Step 3: Commit**

```bash
cd /home/lappemikb/projects/marinade-generator
git add index.html
git commit -m "add display card CSS"
```

---

### Task 4: Implement buildIngredientCards() and buildDisplayCards()

**Files:**
- Modify: `index.html` — add two builder functions in the `<script>` block

- [ ] **Step 1: Add buildIngredientCards()** in the `<script>` block, before the `generatePDF` function:

```js
function buildIngredientCards(items) {
  const pages = [];
  for (let i = 0; i < items.length; i += 6) {
    const chunk = items.slice(i, i + 6);
    const cards = chunk.map(p => `
      <div class="icard">
        <div class="icard-header" style="background:${p.color}">
          <div class="icard-name">${p.name}</div>
        </div>
        <div class="icard-body">
          <div class="icard-ingredients">${p.ingredients}</div>
        </div>
      </div>`).join('');
    pages.push(`<div class="print-page" style="--card-count:${chunk.length}">${cards}</div>`);
  }
  return pages.join('');
}
```

- [ ] **Step 2: Add buildDisplayCards()** immediately after `buildIngredientCards()`:

```js
function buildDisplayCards(items) {
  const pages = [];
  for (let i = 0; i < items.length; i += 6) {
    const chunk = items.slice(i, i + 6);
    const cards = chunk.map(p => `
      <div class="scard">
        <div class="scard-stripe" style="background:${p.color}"></div>
        <div class="scard-body">
          <div class="scard-name">${p.name}</div>
          <div class="scard-tagline">${p.tagline}</div>
        </div>
        <div class="scard-footer">
          <div style="display:flex;gap:3mm">
            ${p.suitableFor.map(k => {
              const m = MEATS[k];
              return m ? `<span class="scard-emoji">${m.icon}</span>` : '';
            }).join('')}
          </div>
          <div class="scard-badges">
            <span class="scard-badge">Laktoositon</span>
            <span class="scard-badge">Gluteeniton</span>
          </div>
        </div>
      </div>`).join('');
    pages.push(`<div class="print-page" style="--card-count:${chunk.length}">${cards}</div>`);
  }
  return pages.join('');
}
```

- [ ] **Step 3: Verify no JS errors**

Reload `http://localhost:7700`. Open DevTools console. No errors should appear. The print button will still be broken — that's fixed in the next task.

- [ ] **Step 4: Commit**

```bash
cd /home/lappemikb/projects/marinade-generator
git add index.html
git commit -m "add buildIngredientCards and buildDisplayCards functions"
```

---

### Task 5: Update generatePDF() to use both builders

**Files:**
- Modify: `index.html` — replace body of `generatePDF()`

- [ ] **Step 1: Replace the generatePDF() function** with:

```js
function generatePDF() {
  if (!selected.size) { alert('Valitse ensin vähintään yksi tuote.'); return; }

  const items = products.filter(p => selected.has(p.id));
  const printArea = document.getElementById('print-area');
  printArea.innerHTML = buildIngredientCards(items) + buildDisplayCards(items);

  window.onafterprint = () => { printArea.innerHTML = ''; };
  window.print();
}
```

- [ ] **Step 2: Commit**

```bash
cd /home/lappemikb/projects/marinade-generator
git add index.html
git commit -m "update generatePDF to output ingredient and display card sections"
```

---

### Task 6: Verify end-to-end in browser print preview

**Files:** none — manual verification only

- [ ] **Step 1: Select a few products and open print preview**

Go to `http://localhost:7700`. Select 3–4 products using the checkboxes. Click "🖨️ Tulosta / PDF". The browser print dialog should open.

- [ ] **Step 2: Check ingredient pages**

First pages should show white cards, stacked flush, no rounding. Each card: colored header with product name in small caps, white body with the full ingredient list. Cards should divide the page height equally.

- [ ] **Step 3: Check display pages**

Pages after the ingredient pages should show dark (`#181818`) cards, flush, no rounding. Each card: thin colored top stripe, name in small caps white, dimmed italic tagline, footer with grayscale emoji + badge pills.

- [ ] **Step 4: Test with different counts**

- Select 1 product → ingredient page: 1 card fills the page; display page: same
- Select 6 products → 1 page each, 6 equal-height cards
- Select 7 products → 2 ingredient pages (6+1) + 2 display pages (6+1)

- [ ] **Step 5: Verify print-area clears after closing print dialog**

After closing the print dialog without printing, check that `#print-area` is empty (grid is visible again, no leftover print HTML).

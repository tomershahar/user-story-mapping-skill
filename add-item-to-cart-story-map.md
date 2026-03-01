```
USER STORY MAP: Add Item to Cart
Persona: General Consumer

BACKBONE (User Journey)
═════════════════════════════════════════════════════════════════════════════

┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│  1. Browse  │  │  2. View    │  │  3. Select  │  │  4. Add to  │  │  5. View    │
│   Listings  │  │   Product   │  │   Options   │  │    Cart     │  │Confirmation │
└──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘
       │                │                │                │                │
───────┼────────────────┼────────────────┼────────────────┼────────────────┼─── R1 (MVP)
       │                │                │                │                │
  ┌────┴────┐      ┌────┴────┐      ┌────┴────┐      ┌────┴────┐      ┌────┴────┐
  │See stock│      │Product  │      │Select   │      │Add to   │      │Confirm- │
  │status on│      │details +│      │variant  │      │cart btn │      │ation    │
  │listing  │      │price    │      │qty      │      │⚠️ race  │      │+ cart   │
  └─────────┘      └─────────┘      └─────────┘      │condition│      │count    │
                   ┌─────────┐      ┌─────────┐      └─────────┘      └─────────┘
                   │Out of   │      │Enforce  │      ┌─────────┐      ┌─────────┐
                   │stock    │      │inventory│      │Out of   │      │Cart     │
                   │state    │      │limit    │      │stock    │      │persists │
                   │⚠️ UX    │      │⚠️ edge  │      │error    │      │across   │
                   └─────────┘      └─────────┘      └─────────┘      │sessions │
                                    ┌─────────┐                        └─────────┘
                                    │Variant  │
                                    │not      │
                                    │selected │
                                    │error    │
                                    └─────────┘
       │                │                │
───────┼────────────────┼────────────────┼────────────────────────────────────── R2
       │                │                │
  ┌────┴────┐      ┌────┴────┐      ┌────┴────┐
  │"Only N  │      │Recently │      │Edit qty │
  │left"    │      │viewed   │      │remove   │
  │badge    │      │items    │      │item in  │
  └─────────┘      └─────────┘      │cart     │
                                    └─────────┘
───────┼──────────────────────────────────────────────────────────────────── Future
       │
  ┌────┴────┐
  │Save for │
  │later /  │
  │wishlist │
  └─────────┘

Legend: 💬 Comment  📝 Task  ⚠️ Risk
```

---

## Story Map: Add Item to Cart

### Persona
**Primary:** General Consumer — a shopper browsing an e-commerce app who wants to select and reserve items before checkout. Must be logged in to use the cart.

---

### Backbone

#### Activity 1: Browse Listings
> Consumer browses product listings and gets a sense of availability before clicking through.

##### R1 (MVP)

**Story 1.1: See stock status on listing**
- **User Story:** As a consumer, I want to see whether an item is in stock from the listing view, so I don't waste time clicking into unavailable products.
- **Acceptance Criteria:**
  - [ ] Given a product is in stock, then no stock indicator is shown (default available state).
  - [ ] Given a product is out of stock, then an "Out of stock" label is shown on the listing card.
- **Tasks:**
  - [ ] Display out-of-stock badge on listing cards

##### R2

**Story 1.2: Low stock badge**
- **User Story:** As a consumer, I want to see when only a few items are left, so I can act quickly on items I want.
- **Acceptance Criteria:**
  - [ ] Given a product has 3 or fewer units remaining, then a "Only N left" badge is shown on the listing card.
- **Comments:** Threshold (3 units) is an assumption — confirm with product.

---

#### Activity 2: View Product Page
> Consumer opens a product page to see full details and decide whether to buy.

##### R1 (MVP)

**Story 2.1: View product details and price**
- **User Story:** As a consumer, I want to see full product details and the current price, so I can make an informed decision.
- **Acceptance Criteria:**
  - [ ] Given I open a product page, then I see name, description, price, images, and available variants.
  - [ ] Given a product is in stock, then the "Add to Cart" button is enabled.

**Story 2.2: Out of stock state**
- **User Story:** As a consumer, I want to clearly see when a product is unavailable, so I don't waste time trying to buy something I can't get.
- **Acceptance Criteria:**
  - [ ] Given a product is out of stock, then the "Add to Cart" button is disabled and an "Out of stock" message is shown.
- **Risks:** UX risk — disabled button alone is not enough. Must show a clear message explaining why.

##### R2

**Story 2.3: Recently viewed items**
- **User Story:** As a consumer, I want to see items I recently viewed, so I can easily return to products I was considering.
- **Acceptance Criteria:**
  - [ ] Given I have viewed 2+ products, then a "Recently viewed" section appears on product pages.

---

#### Activity 3: Select Options
> Consumer selects variant (size, color, etc.) and quantity before adding to cart.

##### R1 (MVP)

**Story 3.1: Select variant and quantity**
- **User Story:** As a consumer, I want to select the right variant and quantity before adding to cart, so I get exactly what I want.
- **Acceptance Criteria:**
  - [ ] Given a product has variants, when I open the product page, then I must select a variant before the "Add to Cart" button is active.
  - [ ] Given I select a variant, then I can choose a quantity defaulting to 1.

**Story 3.2: Enforce inventory limit**
- **User Story:** As a consumer, I want to be prevented from adding more units than are available, so I don't get a failed order later.
- **Acceptance Criteria:**
  - [ ] Given a product has N units in stock, then the quantity selector is capped at N.
  - [ ] Given I already have M units in my cart, then I can only add up to N-M more.
- **Risks:** Edge case — stock count may change between page load and add-to-cart. Must re-validate at add time.

**Story 3.3: Variant not selected error**
- **User Story:** As a consumer, I want a clear prompt if I try to add to cart without selecting a required variant, so I know what I'm missing.
- **Acceptance Criteria:**
  - [ ] Given a product requires variant selection, when I tap "Add to Cart" without selecting, then a validation message highlights the missing selection.

##### R2

**Story 3.4: Edit quantity / remove from cart**
- **User Story:** As a consumer, I want to change the quantity or remove an item from my cart, so I can adjust my order before checkout.
- **Acceptance Criteria:**
  - [ ] Given I am viewing my cart, then I can increase or decrease item quantity within inventory limits.
  - [ ] Given I set quantity to 0 or tap remove, then the item is removed from the cart.

---

#### Activity 4: Add to Cart
> Consumer taps "Add to Cart" — the item is reserved and cart state is updated.

##### R1 (MVP)

**Story 4.1: Add item to cart**
- **User Story:** As a consumer, I want to add an item to my cart with one tap, so I can continue shopping and check out later.
- **Acceptance Criteria:**
  - [ ] Given I have selected all required options, when I tap "Add to Cart", then the item is added and the cart icon count updates immediately.
  - [ ] Given the item is successfully added, then I see a confirmation (toast or bottom sheet).

**Story 4.2: Out of stock at add time**
- **User Story:** As a consumer, I want a clear error if an item sells out between me viewing it and tapping "Add to Cart", so I'm not left confused.
- **Acceptance Criteria:**
  - [ ] Given an item sells out after I loaded the page, when I tap "Add to Cart", then I see an "Item is no longer available" error and the button is disabled.
- **Risks:** Inventory race condition — two users buying the last unit simultaneously. Must re-check stock server-side at add time, not just at page load.

---

#### Activity 5: View Confirmation
> Consumer sees feedback that the item was added and can continue shopping or proceed to checkout.

##### R1 (MVP)

**Story 5.1: Add to cart confirmation**
- **User Story:** As a consumer, I want instant confirmation that my item was added to cart, so I know the action worked.
- **Acceptance Criteria:**
  - [ ] Given an item is added successfully, then a confirmation toast or bottom sheet appears showing the item name and current cart total.
  - [ ] Given the confirmation is shown, then I can dismiss it and continue shopping or navigate to the cart.

**Story 5.2: Cart persists across sessions**
- **User Story:** As a consumer, I want my cart to still be there when I return to the app, so I don't have to start over.
- **Acceptance Criteria:**
  - [ ] Given I add items to my cart and close the app, when I return, then my cart items are still present.
  - [ ] Given my session expires, when I log back in, then my cart is restored.
- **Risks:** Cart persistence is tied to the user account — items are stored server-side, not just locally.

---

### Release Summary

| Release | Scope | Stories | Goal |
|---------|-------|---------|------|
| R1 (MVP) | Core add-to-cart flow | 1.1, 2.1, 2.2, 3.1, 3.2, 3.3, 4.1, 4.2, 5.1, 5.2 | Consumer can find, select, and add an item to a persistent cart with full inventory enforcement |
| R2 | Enhanced browsing & cart management | 1.2, 2.3, 3.4 | Low stock signals, recently viewed, edit/remove cart items |
| Future | Wishlist | — | Save for later / wishlist functionality |

---

### Open Risks

| ID | Story | Risk | Severity | Status |
|----|-------|------|----------|--------|
| R1 | 4.2 | Inventory race condition — stock must be re-validated server-side at add time | High | Open |
| R2 | 2.2 | Disabled button alone is insufficient UX — needs clear out-of-stock message | Medium | Open |
| R3 | 3.2 | Cart quantity must account for units already in cart, not just total stock | Medium | Open |
| R4 | 5.2 | Cart persistence requires server-side storage tied to user account | Medium | Open |

### Open Tasks

| ID | Story | Task | Owner | Status |
|----|-------|------|-------|--------|
| T1 | 1.1 | Display out-of-stock badge on listing cards | — | Todo |
| T2 | 3.2 | Cap quantity selector based on available stock minus cart quantity | — | Todo |
| T3 | 4.1 | Update cart icon count optimistically on add | — | Todo |
| T4 | 4.2 | Server-side stock re-validation on add-to-cart request | — | Todo |
| T5 | 5.2 | Implement server-side cart persistence linked to user account | — | Todo |

---

### Assumptions to Confirm

1. Products can have variants (size/color) — variant selection is required before add. If products are variant-free, stories 3.1 and 3.3 simplify significantly.
2. Low stock threshold = 3 units for the "Only N left" badge (R2).
3. Cart confirmation is a toast/bottom sheet, not a full page redirect.

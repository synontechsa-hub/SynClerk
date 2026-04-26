# SynClerk
### Codename: SynClerk | Final Name: TBD
### *"Professional invoicing for every small business — free, local, beautiful."*

> **Reference:** This plan is derived from the SynClerk Design Bible v0.1.
> When this plan and the design bible conflict, the design bible is the source of truth.
> Update both documents together when decisions change.

---

## 🧠 What Is This?

SynClerk is a lightweight, beautiful, cross-platform invoice and quote management application built for small businesses that cannot afford enterprise accounting software.

It is not an accounting suite. It is not a subscription service. It is not Xero, QuickBooks, or Sage.

It is the tool a workshop owner, freelancer, or tradesperson actually needs — quotes, invoices, clients, and clean PDFs — without the bloat, the monthly bill, or the 2004 UI.

**Local-first. Offline-capable. Free at its core. Beautiful by default.**

---

## 🏷️ Identity

| | |
|---|---|
| **Codename** | SynClerk |
| **Final name** | TBD — research during dev cycle |
| **Tagline** | *"Professional invoicing for every small business"* |
| **Developer** | Syn — SynonTech, Johannesburg, South Africa |
| **Stack** | Tauri 2.x · Rust · HTML/CSS/Vanilla JS |
| **Target platforms** | Windows, macOS, Linux |
| **Storage (v1)** | JSON flat files — local only |
| **Storage (v2)** | SQLite migration + optional cloud sync |
| **Binary size** | 3–10 MB (Tauri native webview) |
| **Price** | Free — core features always free |
| **PDF credit** | *"Generated with SynClerk · synontech.com"* |

---

## 🎯 Philosophy

- **Local-first** — data lives on the user's machine, always
- **Offline-capable** — no internet required for any core feature
- **Free core** — v1.0 is completely free, cloud sync is a future opt-in
- **Small binary** — Tauri keeps the install under 10 MB
- **Beautiful by default** — small businesses deserve professional-looking software
- **Honest branding** — the PDF footer credit is tasteful, transparent, and never intrusive

---

## ⚙️ Tech Stack

| Layer | Technology | Reason |
|---|---|---|
| Framework | Tauri 2.x | Native webview, tiny binary, cross-platform, Rust backend |
| Backend / Logic | Rust | Performance, memory safety, PDF generation, file I/O |
| Frontend / UI | HTML + CSS + Vanilla JS | Lightweight, no heavy framework overhead |
| PDF Generation | printpdf (Rust crate) | Native Rust, no external dependencies |
| Data Storage (v1) | JSON flat files | Simple, portable, human-readable |
| Data Storage (v2) | SQLite | Robust, faster queries, better for history/analytics |
| Cloud Sync (v2) | TBD — Supabase candidate | Optional — local-first always works without it |
| Build / Dist | Tauri bundler | .exe, .dmg, .AppImage from one codebase |

### Learning Intent
SynClerk is a learning project as much as a product. Syn is learning Rust and Tauri through this build. Architecture must be kept clean and well-commented. Complexity is introduced incrementally — never ahead of need.

**First session goal:** Get a bare Tauri app running that renders "Hello SynClerk." Understand the Rust ↔ JS bridge before building anything real.

---

## 🖥️ Navigation Structure

```
Sidebar (persistent)
├── Dashboard        — summary, overdue alerts (v1.5)
├── Clients          — client list, add/edit/view
├── Quotes           — quote list, filterable by status
├── Invoices         — invoice list, filterable by status
└── Settings         — profile, tax rates, currency, preferences
```

**Document editor:** Full-screen modal or right panel — opens on top of the list view.
**First launch:** No sidebar — app immediately prompts for company profile setup.

---

## 🔄 Core User Flows

### First Launch
```
App opens
  → No company profile detected
  → Profile setup wizard:
      Company name (required)
      Contact details
      Default currency
      Default tax rate
      Logo upload (optional)
  → Profile saved
  → App opens to document list (empty state)
```

### Create a Quote
```
Click New Quote
  → Select existing client OR create new client inline
  → Add line items: description · qty · unit · unit price · discount %
  → Tax auto-applied from profile default (overridable per document)
  → Add client-facing notes (optional)
  → Save as Draft (auto-saves on every change)
  → When ready: mark as Sent → export PDF → share with client
```

### Convert Quote → Invoice
```
Open an Accepted quote
  → Click Convert to Invoice
  → New Invoice created, pre-filled with all quote data
  → New invoice number auto-assigned (INV-XXXX)
  → converted_from field set to original quote UUID
  → Original quote status: unchanged (remains Accepted)
  → User lands on new invoice in Draft state
```

### Mark Invoice as Paid
```
Open invoice
  → Change status to Paid
  → Paid date recorded
  → Invoice moves to Paid filter in list
```

### Load / Share Documents
```
All data in JSON in user's data directory
  → User copies JSON files to share with another SynClerk user
  → Recipient: File → Import
  → App validates and imports document + client data
  → Duplicate detection: warn on matching doc number or UUID
```

---

## 🧩 Feature Specification

| Feature | v1.0 | v1.5 | v2.0 | Notes |
|---|---|---|---|---|
| Company profile setup | ✓ | ✓ | ✓ | Name, logo, address, contact, bank details |
| Multiple user profiles | ✓ | ✓ | ✓ | Each profile = one company / operator |
| Client management | ✓ | ✓ | ✓ | Name, contact, address, client ID |
| Auto-increment client IDs | ✓ | ✓ | ✓ | CLT-0001 format, user-editable |
| Quote creation | ✓ | ✓ | ✓ | Line items: desc, qty, unit, price, discount |
| Invoice creation | ✓ | ✓ | ✓ | Same structure as quote |
| Quote → Invoice conversion | ✓ | ✓ | ✓ | One-click, all data preserved |
| Document status system | ✓ | ✓ | ✓ | 8 statuses — see below |
| Auto-increment doc numbers | ✓ | ✓ | ✓ | INV-0001 / QUO-0001, user-editable |
| Tax rate configuration | ✓ | ✓ | ✓ | User-defined (VAT 15%, GST 10%, etc.) |
| Multi-currency support | ✓ | ✓ | ✓ | Per-client currency assignment |
| PDF export | ✓ | ✓ | ✓ | Professional layout, company branding |
| SynClerk PDF footer credit | ✓ | ✓ | ✓ | Subtle, tasteful, always present in free tier |
| Local file save / load | ✓ | ✓ | ✓ | JSON, portable, human-readable |
| Company logo on PDF | ✓ | ✓ | ✓ | Optional — layout degrades gracefully |
| File sharing between users | ✓ | ✓ | ✓ | JSON portability — copy and import |
| Dashboard with alerts | ✗ | ✓ | ✓ | Overdue alerts, session summary |
| Duplicate document | ✗ | ✓ | ✓ | Clone any quote or invoice |
| Search and filter | ✗ | ✓ | ✓ | Across all documents by status / client / date |
| Keyboard shortcuts | ✗ | ✓ | ✓ | Power user navigation |
| Light / dark mode toggle | ✗ | ✓ | ✓ | Dark mode default in v1.0 |
| Custom accent colour (PDF) | ✗ | ✓ | ✓ | Company brand colour in PDF |
| Purchase Orders (PO) | ✗ | ✗ | ✓ | Same structure as Invoice |
| Cloud sync | ✗ | ✗ | ✓ | Optional, user-controlled, Supabase candidate |
| Email invoice via SMTP | ✗ | ✗ | ✓ | Send directly from app |
| Recurring invoices | ✗ | ✗ | ✓ | Scheduled generation |
| Revenue dashboard | ✗ | ✗ | ✓ | Analytics, charts, reporting |
| Remove footer credit | ✗ | ✗ | ~ | Paid feature candidate |
| Multi-language support | ✗ | ✗ | ~ | Under consideration |
| SQLite migration | ✗ | ✗ | ✓ | Replace JSON flat files |

*✓ included · ✗ not included · ~ under consideration*

---

## 📊 Document Status System

Every document (Quote, Invoice, PO) carries exactly one status at all times.

| Status | Colour | Description |
|---|---|---|
| Draft | Grey | Created but not yet sent |
| Sent | Blue | Issued to client |
| Accepted | Green | Client accepted the quote |
| Declined | Red | Client declined the quote |
| Paid | Dark Green | Invoice paid in full |
| Overdue | Amber | Payment due date passed |
| Partial | Amber | Partial payment received |
| Cancelled | Muted Grey | Voided — kept for records, never deleted |

### Status Transition Rules
- Statuses are **always set manually** by the user
- App may **suggest** status changes (e.g. surface Overdue when due date passes) but never auto-applies them without confirmation
- Quote path: `Draft → Sent → Accepted → (convert to Invoice)` or `Declined / Cancelled`
- Invoice path: `Draft → Sent → Paid / Overdue / Partial / Cancelled`
- Any document can be `Cancelled` at any point — nothing is ever deleted

---

## 📦 Data Models

### Company Profile
```rust
struct CompanyProfile {
    id:               Uuid,
    company_name:     String,
    logo_path:        Option<String>,
    address:          Option<String>,
    phone:            Option<String>,
    email:            Option<String>,
    website:          Option<String>,
    bank_name:        Option<String>,
    bank_account:     Option<String>,
    bank_branch:      Option<String>,
    default_currency: String,       // ISO code: ZAR, USD, EUR etc.
    default_tax_rate: f64,
    tax_rates:        Vec<TaxRate>,
    invoice_prefix:   String,       // default: "INV-"
    quote_prefix:     String,       // default: "QUO-"
    next_invoice_num: u32,
    next_quote_num:   u32,
    created_at:       DateTime<Utc>,
}
```

### Client
```rust
struct Client {
    id:             Uuid,
    client_number:  String,         // e.g. CLT-0001 — user-editable
    name:           String,
    contact_person: Option<String>,
    email:          Option<String>,
    phone:          Option<String>,
    address:        Option<String>,
    currency:       String,
    tax_number:     Option<String>,
    notes:          Option<String>,
    created_at:     DateTime<Utc>,
}
```

### Document (Quote / Invoice)
```rust
struct Document {
    id:               Uuid,
    doc_type:         DocType,      // Quote | Invoice | PurchaseOrder
    doc_number:       String,       // e.g. INV-0042 — user-editable
    status:           DocStatus,    // Draft | Sent | Accepted | Declined | Paid | Overdue | Partial | Cancelled
    client_id:        Uuid,
    profile_id:       Uuid,
    issue_date:       NaiveDate,
    due_date:         Option<NaiveDate>,
    expiry_date:      Option<NaiveDate>,
    line_items:       Vec<LineItem>,
    tax_rate:         f64,
    discount_global:  f64,
    currency:         String,
    notes_internal:   Option<String>,   // not printed on PDF
    notes_client:     Option<String>,   // printed on PDF
    converted_from:   Option<Uuid>,     // set if converted from Quote
    created_at:       DateTime<Utc>,
    updated_at:       DateTime<Utc>,
}
```

### Line Item
```rust
struct LineItem {
    id:           Uuid,
    description:  String,
    quantity:     f64,
    unit_price:   f64,
    discount_pct: f64,
    tax_override: Option<f64>,  // overrides document tax rate for this line
    unit:         Option<String>,   // e.g. "hrs", "m²", "each", "kg"
    sort_order:   u32,
}
```

### Tax Rate
```rust
struct TaxRate {
    id:    Uuid,
    label: String,   // e.g. "VAT", "GST", "No Tax"
    rate:  f64,      // percentage: 15.0 = 15%
}
```

---

## 📄 PDF Layout

Every generated PDF follows this structure, top to bottom:

```
┌─────────────────────────────────────────────────┐
│  [LOGO]          Company Name                   │
│                  Address · Phone · Email        │
├─────────────────────────────────────────────────┤
│  INVOICE                         INV-0042       │
│                         Issue: 2026-04-26       │
│                           Due: 2026-05-26       │
│                        Status: [SENT]           │
├─────────────────────────────────────────────────┤
│  Bill To:                                       │
│  Client Name                                    │
│  Client Address                                 │
│  CLT-0001                                       │
├────────────────┬──────┬────────┬───────┬────────┤
│  Description   │ Qty  │  Unit  │ Price │  Total │
├────────────────┼──────┼────────┼───────┼────────┤
│  Line item...  │  2   │   hrs  │ R500  │ R1000  │
├────────────────┴──────┴────────┴───────┴────────┤
│                              Subtotal:  R1000   │
│                            Discount:     -R0    │
│                           Tax (15%):   +R150    │
│                               TOTAL:  R1150     │
├─────────────────────────────────────────────────┤
│  Payment Details:                               │
│  Bank · Account · Branch                        │
├─────────────────────────────────────────────────┤
│  Notes: [client-facing notes here]              │
├─────────────────────────────────────────────────┤
│  Page 1 of 1     Generated with SynClerk ·     │
│                  synontech.com                  │
└─────────────────────────────────────────────────┘
```

### PDF Rules
- Always A4 format
- Logo is optional — layout degrades gracefully without it
- Status badge printed on document
- SynClerk footer credit: always present in free tier, 8pt, muted grey
- Internal notes (`notes_internal`) are **never** printed
- Client notes (`notes_client`) are printed in the notes section

---

## 🎨 UI Design Direction

### Colour Palette
| Role | Value | Usage |
|---|---|---|
| Background | `#1A1A2E` | App background — deep navy |
| Surface | `#16213E` | Sidebar, cards |
| Border | `#2A2A4A` | Subtle separators |
| Text primary | `#F0F0F0` | Main readable text |
| Text muted | `#8892A4` | Labels, secondary info |
| Accent | `#4F8EF7` | Buttons, highlights, active states |
| Accent dark | `#2B5FD9` | Hover states |
| Success | `#2ECC71` | Paid, Accepted status |
| Warning | `#F39C12` | Overdue, Partial status |
| Danger | `#E74C3C` | Declined, Cancelled status |
| Neutral | `#6B7280` | Draft, muted states |

### Typography
- Font: **Segoe UI** / **Inter** — clean, modern, readable
- Headings: bold, generous sizing
- Body: 14–16px, 1.6 line height
- Monospaced elements (doc numbers, amounts): **JetBrains Mono** or **Courier New**

### Design Principles
- Dark mode default in v1.0 — light mode added in v1.5
- Persistent left sidebar navigation
- Generous whitespace — never cramped
- Every action has visible feedback — toast notifications, status badge updates
- Non-destructive — nothing deleted, only Cancelled
- Auto-save — no manual save needed for drafts

---

## ✅ v1.0 Success Criteria

- First document creatable within 2 minutes of install
- Zero confusion points — a non-technical small business owner can use it without help
- Quote → Invoice conversion works end-to-end correctly
- PDF export produces a clean, professional, correctly structured A4 document
- SynClerk footer credit present on all PDFs
- All 8 document statuses function correctly
- Client IDs and document numbers auto-increment reliably
- JSON save / load / import works without data loss
- Multi-currency — amounts display in the correct currency per client
- User-defined tax rates apply correctly to line item totals
- Cross-platform binary builds successfully on Windows, macOS, and Linux

---

## 🚀 Version Roadmap

### v1.0 — Foundation
*Target: 2–4 months of side-project development*

- [ ] Bare Tauri app running — "Hello SynClerk" — understand the bridge
- [ ] Project structure and Rust module layout defined
- [ ] Company profile setup (first-launch wizard)
- [ ] Client management — add, edit, view, client IDs
- [ ] Quote creation with line items
- [ ] Invoice creation with line items
- [ ] Quote → Invoice conversion
- [ ] Document status system (all 8 statuses)
- [ ] Auto-increment document numbers (user-editable)
- [ ] User-defined tax rates
- [ ] Multi-currency support (per-client)
- [ ] PDF export — full layout with branding
- [ ] SynClerk footer credit on all PDFs
- [ ] Company logo upload and embed in PDF
- [ ] JSON save and load
- [ ] File import / share between users
- [ ] Cross-platform binary (Windows, macOS, Linux)
- [ ] Dark mode UI — full implementation

### v1.5 — Polish
*Post-launch, based on real user feedback*

- [ ] Dashboard — document summary, overdue alerts
- [ ] Duplicate document function
- [ ] Search and filter across all documents
- [ ] Keyboard shortcuts for common actions
- [ ] Light / dark mode toggle
- [ ] Custom company accent colour in generated PDFs
- [ ] Improved empty states and onboarding hints

### v2.0 — Expansion
*Future — after v1.5 is stable*

- [ ] Purchase Orders (PO) — same structure as Invoice
- [ ] Cloud sync — optional, user-controlled, Supabase candidate
- [ ] Email invoice directly via SMTP integration
- [ ] Recurring invoice scheduling
- [ ] Revenue dashboard and analytics
- [ ] Remove SynClerk footer credit — paid feature candidate
- [ ] Multi-language / localisation support
- [ ] SQLite migration from JSON flat files
- [ ] Mobile web optimisation (stretch goal)

---

## 🧭 Build Order (v1.0 Recommended Sequence)

This sequence minimises blockers and builds on itself logically:

1. **Tauri scaffold** — bare app, understand project structure and Rust ↔ JS bridge
2. **Data layer** — define all Rust structs, JSON serialisation/deserialisation
3. **Company profile** — first-launch wizard, settings screen
4. **Client management** — CRUD, auto-increment IDs
5. **Document creation** — quote and invoice editor, line items
6. **Status system** — implement all 8 statuses, transitions
7. **Quote → Invoice conversion** — end-to-end flow
8. **PDF export** — Rust printpdf integration, full layout
9. **Save / load / import** — JSON file handling, import validation
10. **UI polish** — sidebar, navigation, dark theme, responsive layout
11. **Cross-platform build** — test and fix on Windows, macOS, Linux
12. **v1.0 release**

---

## 🔤 Naming & Branding

### Codename
**SynClerk** — internal development name only. Follows SynonTech convention (SoulLink, SynCAD, SynQuest, SynTime). Never shown to end users.

### Final Name Research Criteria
- Available as a `.com` domain
- Not trademarked in software / SaaS space
- Short, memorable, hints at invoicing or business
- Works globally — no region-specific slang
- SynClerk is a strong candidate if research clears it

### Brand Voice
Direct. Clear. Professional without being cold. Speaks to small business owners as equals — not as a tech company explaining things to non-technical users.

- No jargon
- Confident but not arrogant
- Helpful without being patronising
- Never talks down

### PDF Credit (All v1.0 documents)
```
Generated with SynClerk · synontech.com
```
8pt · Muted grey · Bottom right of PDF footer · Always present in free tier

---

## 🗂️ Project Pipeline Context

SynClerk is a **side project** developed between SoulLink sessions. It is not the primary focus.

| Project | Status | Role |
|---|---|---|
| KerfCut (SynCAD) | Active — near completion | Current main focus |
| SoulLink | Active — ongoing | Primary long-term project |
| SynClerk | Planned | Side project between SoulLink sessions |
| SynTime | Planned | Separate side project — lighter build |

Development philosophy: **pick it up, make progress, put it down.** The build order above and this plan exist so that context-switching back into SynClerk after a SoulLink sprint costs zero ramp-up time.

---

*SynClerk Development Plan · Derived from Design Bible v0.1 · SynonTech*
*Codename only — final product name TBD*

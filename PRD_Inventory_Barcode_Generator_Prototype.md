# Product Requirements Document (PRD)

## Inventory Barcode Generator with Asset Tracking — Working Prototype

**Version:** 1.0 (Prototype MVP)  
**Date:** May 30, 2026  
**Status:** Draft for Review  
**Prepared for:** Product & Engineering Team  

---

## 1. Document Purpose

This PRD defines the requirements for a **working prototype** (not the full product) of the Inventory Barcode Generator with Asset Tracking system. The prototype is designed to validate core assumptions with four real user personas identified through LinkedIn profile analysis and market research.

**Prototype Scope:** 6-week build, 3-week pilot with 2-3 beta users per persona segment  
**Success Criteria:** 80% task completion rate, <5 minute setup time, NPS ≥ 40  

---

## 2. Executive Summary

### 2.1 Product Vision

Enable small businesses (100-5,000 SKUs) transitioning from manual/spreadsheet inventory tracking to their first barcode system — without expensive software, dedicated hardware, or data migration.

### 2.2 Prototype Hypothesis

> *If we provide a three-step setup (upload → generate → print) with Google Sheets as the persistent data layer and smartphone-based scanning, then small business owners will adopt barcode inventory management within 30 minutes without IT support.*

### 2.3 Target Segments (from Persona Analysis)

| Segment | Persona | Primary Pain | Decision Speed | Price Sensitivity |
|---------|---------|-------------|---------------|-------------------|
| Small Retail | Koteswara Rao (KD) | Multi-category chaos | Fast | Very High |
| Equipment Rental | Indrajit Paul (IP) | Asset visibility across sites | Slow | Low (enterprise) |
| Startup Operations | Samrudhi Sudhakar (SS) | Rapid scaling breaking systems | Medium | Low |
| Medical Administration | Andrea Bissonnette (AB) | Compliance + audit readiness | Very Slow | Medium |

### 2.4 Prototype vs. Full Product Scope

| Feature Area | Prototype (MVP) | Full Product (Post-Validation) |
|-------------|-----------------|------------------------------|
| Barcode Formats | Code 128 + QR Code | Code 128, QR, Data Matrix, UPC/EAN |
| Label Formats | PDF (A4/Letter) + PNG (thermal) | PDF, PNG, ZPL, DYMO, Brother |
| Sync Backend | Direct Google Sheets API | Hybrid backend + Redis queue |
| User Roles | Single user + read-only sharing | 3-tier RBAC (Admin/Manager/Operator) |
| Offline Mode | Basic queued sync | Full offline with conflict resolution |
| Integrations | Google Sheets only | Airtable, Firebase, Slack, QuickBooks |
| Analytics | Basic turnover report | Full dashboard with predictive alerts |

---

## 3. User Personas & Prototype Scenarios

### 3.1 Persona 1: Koteswara Rao Devarasetty (KD)
**Segment:** Small Retail Owner  
**Company:** BSKB Communications, Guntur, India  
**Scenario:** Track 500+ SKUs across mobiles, TVs, laptops, ACs, accessories, and repair parts

#### Prototype Success Criteria for KD:
- Upload existing Google Sheet with 500+ rows in <2 minutes
- Generate barcodes for all items in <10 minutes
- First inventory count completed in <1 hour (vs. 4 hours manual)
- Discover at least 5 discrepancies on first scan (validates value)

#### Critical Path:
```
Google Workspace Marketplace discovery → SSO signup → Sheet upload → 
Barcode generation (batch) → PDF label print → Mobile PWA scan → 
Discrepancy discovery → Google Sheets sync verification
```

---

### 3.2 Persona 2: Indrajit Paul (IP)
**Segment:** Equipment Rental / Enterprise Asset Management  
**Company:** Larsen & Toubro, Chennai, India  
**Scenario:** Pilot digital asset tracking for 50 rental equipment units across 3 project sites

#### Prototype Success Criteria for IP:
- Upload CSV extract from SAP/ERP in <5 minutes
- Configure 4 custom fields (Asset ID, Site, Rental Status, Maintenance Due)
- Deploy to 3 sites with 90%+ daily scan compliance within 1 week
- Generate first utilization report showing >70% actual vs. estimated

#### Critical Path:
```
Industry conference demo → Pilot approval → SAP CSV upload → 
Custom field mapping → ZPL/thermal label generation → Multi-site deployment → 
Daily scan dashboard → Weekly utilization report
```

---

### 3.3 Persona 3: Samrudhi Sudhakar (SS)
**Segment:** Startup Operations / Demo Unit Tracking  
**Company:** upliance.ai, Bengaluru, India  
**Scenario:** Track 45 demo units across review, event, internal, and repair categories

#### Prototype Success Criteria for SS:
- Create 5 custom categories with auto due-dates in <10 minutes
- QR Code generation (brand-aligned, modern aesthetic)
- 100% team adoption (5+ members scanning within 48 hours)
- Zero lost demo units during 4-week pilot

#### Critical Path:
```
Product Hunt discovery → Slack team vote → Google SSO → 
Demo unit spreadsheet upload → Category/due-date configuration → 
QR label generation (Brother QL-800) → Team labeling party → 
Check-out/check-in workflow → Weekly founder report
```

---

### 3.4 Persona 4: Andrea Bissonnette (AB)
**Segment:** Medical Office / Vaccine Inventory  
**Company:** Carleton University, Ottawa, Canada  
**Scenario:** Track 23 vaccine types, 150+ lots, with expiration alerts and audit compliance

#### Prototype Success Criteria for AB:
- Upload vaccine inventory with lot numbers and expiry dates in <5 minutes
- Configure 3-tier alert system (30/14/0 days) with color coding
- Pass mock audit with 100% lot traceability and timestamp verification
- Train 8 nurses to scan-before-administer in <20 minutes

#### Critical Path:
```
Health conference session → IT security review (3 weeks) → 
Vaccine inventory upload → Expiration alert configuration → 
Role-based access (Admin/Manager/Operator) → Small label generation (1"×0.5") → 
Clinical staff training → Flu clinic 312-scan event → Audit report generation
```

---

## 4. Functional Requirements

### 4.1 Core Feature: Three-Step Setup

#### FR-1.1: Spreadsheet Upload
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-1.1.1 | Support Google Sheets direct import via API | P0 | OAuth2 connection, <30s for 1000 rows |
| FR-1.1.2 | Support CSV/Excel file upload | P0 | Drag-drop, auto-detect headers, preview mapping |
| FR-1.1.3 | Auto-detect column types (name, SKU, qty, location, category) | P1 | 80% accuracy on standard headers |
| FR-1.1.4 | Manual column mapping for non-standard headers | P1 | Dropdown mapping interface, save template |
| FR-1.1.5 | Validate data integrity (duplicate SKUs, negative quantities) | P1 | Highlight errors, suggest fixes |

#### FR-1.2: Barcode Generation
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-1.2.1 | Generate Code 128 barcodes with custom prefix | P0 | Prefix validation, sequential numbering |
| FR-1.2.2 | Generate QR Code as secondary format | P0 | Toggle between formats, same data payload |
| FR-1.2.3 | Batch generation for 500+ items in <10 minutes | P0 | Progress indicator, background processing |
| FR-1.2.4 | Support custom fields in barcode payload (up to 4 fields) | P1 | Field selector, payload preview |
| FR-1.2.5 | Generate barcode reference sheet (index page) | P2 | Printable A4/Letter summary |

#### FR-1.3: Label Printing
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-1.3.1 | PDF output for standard printers (A4/Letter) | P0 | Avery 5160 template, customizable layout |
| FR-1.3.2 | PNG output for thermal printers (300 DPI) | P1 | 2"×1", 4"×6" presets, monochrome |
| FR-1.3.3 | Label layout editor (product name, SKU, barcode, location) | P1 | Drag-drop fields, font size control |
| FR-1.3.4 | Print calibration wizard for alignment | P2 | Test page, margin adjustment, save profile |

---

### 4.2 Core Feature: Mobile PWA Scanner

#### FR-2.1: Scanning Capability
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-2.1.1 | Scan Code 128 and QR Code using smartphone camera | P0 | <2s scan time, 95% accuracy in good light |
| FR-2.1.2 | Auto-focus and torch/flash control | P0 | Toggle button, auto-enable in low light |
| FR-2.1.3 | Manual barcode entry fallback with validation | P1 | Prefix check, format validation, error feedback |
| FR-2.1.4 | Batch scan mode (continuous scanning without confirmation) | P1 | Toggle mode, audio confirmation, count display |

#### FR-2.2: Inventory Operations
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-2.2.1 | Check-in/check-out with timestamp and user ID | P0 | One-tap operation, auto-populate fields |
| FR-2.2.2 | Location transfer with from/to logging | P0 | Dropdown locations, scan confirmation |
| FR-2.2.3 | Quantity adjustment (increment/decrement/set) | P0 | Numeric input, reason code, audit trail |
| FR-2.2.4 | View item details (name, current qty, location, history) | P1 | Expandable card, last 5 transactions |

#### FR-2.3: Offline & Sync
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-2.3.1 | Queue scans locally when offline (IndexedDB) | P0 | Store 500+ scans, visual offline indicator |
| FR-2.3.2 | Auto-sync when connection restored | P0 | Background sync, conflict alert, retry logic |
| FR-2.3.3 | Sync status dashboard (pending/complete/failed) | P1 | Count badge, retry button, error details |

---

### 4.3 Core Feature: Google Sheets Integration

#### FR-3.1: Real-Time Sync
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-3.1.1 | Two-way sync: app writes to Sheet, Sheet changes reflect in app | P0 | <30s latency, conflict detection |
| FR-3.1.2 | Add system columns (barcode, last_scan_date, current_location) | P0 | Auto-append, preserve existing data |
| FR-3.1.3 | Handle Google Sheets API rate limits (300 req/min) | P0 | Batch writes (50 rows), exponential backoff |
| FR-3.1.4 | Data validation and error handling | P1 | Highlight sync failures, suggest fixes |

#### FR-3.2: Sheet Management
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-3.2.1 | Create new inventory Sheet from template | P1 | Pre-populated headers, example row |
| FR-3.2.2 | Connect to existing Sheet (read-write) | P0 | OAuth consent, scope verification |
| FR-3.2.3 | Disconnect and data portability (export CSV) | P1 | Full export, no data lock-in |

---

### 4.4 Prototype-Specific Features (Persona-Driven)

#### FR-4.1: For KD (Small Retail) — Multi-Category Support
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-4.1.1 | Category-based barcode prefix auto-assignment | P1 | Mobiles=MOB, TVs=TV, ACs=AC, etc. |
| FR-4.1.2 | Category-specific reorder point alerts | P2 | Default thresholds, customizable per category |
| FR-4.1.3 | Service parts tracking (repair inventory) | P2 | Separate workflow, consumption logging |

#### FR-4.2: For IP (Enterprise Pilot) — Custom Fields & Reporting
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-4.2.1 | Up to 10 custom fields per item | P1 | Text, number, date, dropdown types |
| FR-4.2.2 | Custom field mapping from CSV upload | P1 | Auto-detect + manual override |
| FR-4.2.3 | Basic utilization report (scan frequency by asset) | P2 | Weekly email, CSV export |
| FR-4.2.4 | Maintenance due date tracking | P2 | Date field, 7-day advance alert |

#### FR-4.3: For SS (Startup) — Team & Event Workflows
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-4.3.1 | Multi-user scan with user attribution | P1 | Google account login, scan log shows user |
| FR-4.3.2 | Check-out with automatic due-date | P1 | Configurable default (7 days review, 1 day event) |
| FR-4.3.3 | Overdue alert (email + in-app) | P1 | Daily digest, individual reminders |
| FR-4.3.4 | Location transfer for events | P1 | "Event" location type, auto-return date |

#### FR-4.4: For AB (Medical) — Expiration & Compliance
| ID | Requirement | Priority | Acceptance Criteria |
|----|-------------|----------|---------------------|
| FR-4.4.1 | Expiration date field with 3-tier alerts | P1 | 30 days (green), 14 days (amber), 0 days (red) |
| FR-4.4.2 | Lot number tracking and traceability | P1 | Unique lot field, scan shows lot + expiry |
| FR-4.4.3 | "Use first" tray flag for near-expiry items | P2 | Auto-flag, visual indicator in app |
| FR-4.4.4 | Audit trail report (user, timestamp, action, lot) | P2 | Filterable by date range, CSV/PDF export |
| FR-4.4.5 | Scan-before-administer confirmation | P2 | Voice confirmation, wrong item warning |

---

## 5. Non-Functional Requirements

### 5.1 Performance

| ID | Requirement | Target | Measurement |
|----|-------------|--------|-------------|
| NFR-1 | Page load time (initial) | <3s | Lighthouse Performance Score ≥ 90 |
| NFR-2 | Barcode generation (500 items) | <10 min | Server-side processing, progress API |
| NFR-3 | Scan-to-sync latency (online) | <5s | End-to-end with Google Sheets API |
| NFR-4 | Offline scan queue capacity | 500 scans | IndexedDB storage limit |
| NFR-5 | Concurrent users (prototype) | 10 users | Load testing with Artillery/K6 |

### 5.2 Security & Compliance

| ID | Requirement | Priority | Notes |
|----|-------------|----------|-------|
| NFR-6 | Google OAuth 2.0 with minimal scopes | P0 | `drive.file` only, no broad Drive access |
| NFR-7 | Data encryption in transit (TLS 1.3) | P0 | Certificate pinning for API |
| NFR-8 | No persistent storage of user inventory data | P0 | All data in user's Google Drive |
| NFR-9 | HIPAA/FIPPA compliance documentation | P2 | For AB segment, pre-built compliance pack |
| NFR-10 | Audit logging (admin actions) | P1 | 90-day retention, exportable |

### 5.3 Usability

| ID | Requirement | Target | Measurement |
|----|-------------|--------|-------------|
| NFR-11 | Setup time (upload to first scan) | <30 min | Unmoderated usability test, n=5 per persona |
| NFR-12 | Task success rate (core flows) | ≥80% | First-attempt completion |
| NFR-13 | System Usability Scale (SUS) score | ≥70 | Post-test questionnaire |
| NFR-14 | Mobile PWA install prompt | P1 | Chrome/Edge/Safari add-to-home-screen |
| NFR-15 | Accessibility (WCAG 2.1 AA) | P2 | Screen reader compatible, color-blind safe |

### 5.4 Reliability

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-16 | Uptime (prototype phase) | 99.5% |
| NFR-17 | Google Sheets API error recovery | Auto-retry 3× with backoff |
| NFR-18 | Data backup (user responsibility) | Google Drive version history |
| NFR-19 | graceful degradation ( Sheets unavailable) | Queue all operations, notify user |

---

## 6. Technical Architecture (Prototype)

### 6.1 System Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │   Web App    │  │  Mobile PWA  │  │  Label Print (PDF)   │  │
│  │  (React/Vue) │  │ (Camera +    │  │  (Browser Print)     │  │
│  │              │  │  IndexedDB)  │  │                      │  │
│  └──────┬───────┘  └──────┬───────┘  └──────────┬───────────┘  │
└─────────┼──────────────────┼─────────────────────┼──────────────┘
          │                  │                     │
          └──────────────────┼─────────────────────┘
                             │ HTTPS/JSON
┌────────────────────────────┼───────────────────────────────────┐
│                      API LAYER (Node.js/Express)               │
│  ┌──────────────┐  ┌──────────────┐  ┌────────────────────┐  │
│  │  Auth Service │  │  Barcode Gen  │  │   Sync Engine      │  │
│  │  (Google OAuth)│  │  (bwip-js)   │  │ (Batch + Retry)    │  │
│  └──────┬───────┘  └──────┬───────┘  └─────────┬──────────┘  │
│         │                 │                    │              │
│  ┌──────┴──────┐  ┌──────┴──────┐  ┌─────────┴──────────┐  │
│  │  User Mgmt  │  │  PDF/PNG Gen  │  │  Webhook Handler   │  │
│  │  (JWT)      │  │  (PDFKit/Sharp)│  │  (Google Sheets)   │  │
│  └─────────────┘  └─────────────┘  └────────────────────┘  │
└───────────────────────────────────────────────────────────────┘
                             │
          ┌──────────────────┼──────────────────┐
          │                  │                  │
┌─────────┴────────┐  ┌────┴────────┐  ┌─────┴─────────┐
│   Google Sheets    │  │   Redis     │  │   PostgreSQL  │
│   (User Data)      │  │  (Queue +   │  │  (Metadata +  │
│   Source of Truth   │  │   Cache)    │  │   Audit Log)  │
└────────────────────┘  └─────────────┘  └───────────────┘
```

### 6.2 Technology Stack (Prototype)

| Layer | Technology | Rationale |
|-------|-----------|-----------|
| Frontend | React 18 + TypeScript + Tailwind CSS | Rapid development, component reuse |
| Mobile PWA | React + Workbox (service worker) | Offline support, installable |
| Barcode Generation | bwip-js (Code 128 + QR) | Client-side generation, no server dependency |
| Backend API | Node.js 20 + Express + TypeScript | Familiar stack, Google API client maturity |
| Database | PostgreSQL 15 (Neon/Supabase) | User metadata, audit logs, config |
| Cache/Queue | Redis (Upstash) | Sync batching, rate limit handling |
| Auth | Google OAuth 2.0 (passport-google-oauth) | Native Sheets integration, SSO |
| PDF Generation | PDFKit + Sharp (PNG) | Server-side rendering, thermal printer support |
| Hosting | Vercel (frontend) + Railway/Render (API) | Rapid deploy, auto-scaling |
| Monitoring | LogRocket + Sentry | Session replay, error tracking |

### 6.3 Google Sheets API Strategy

**Rate Limit Handling:**
- Batch writes: 50 rows per batchUpdate request
- Exponential backoff: 200ms → 800ms → 2500ms for 429 errors
- Queue depth monitoring: Alert at >100 pending operations
- Fallback: Queue to Redis if Sheets API unavailable >5 min

**Data Model:**
```
User Sheet (Source of Truth):
┌─────────┬──────────┬────────┬──────────┬───────────┬─────────────┬─────────────────┐
│ Item_ID │   Name   │  SKU   │ Category │  Qty      │  Barcode    │  Last_Scan_Date │
├─────────┼──────────┼────────┼──────────┼───────────┼─────────────┼─────────────────┤
│  1001   │ iPhone 15│ MOB001 │ Mobiles  │    12     │ BSKB-1001   │  2026-05-28     │
│  1002   │ Samsung  │ MOB002 │ Mobiles  │     8     │ BSKB-1002   │  2026-05-29     │
│  ...    │   ...    │  ...   │   ...    │   ...     │    ...      │      ...        │
└─────────┴──────────┴────────┴──────────┴───────────┴─────────────┴─────────────────┘
```

---

## 7. User Interface Requirements

### 7.1 Key Screens (Prototype)

#### Screen 1: Dashboard (Post-Setup)
```
┌─────────────────────────────────────────────────────────────┐
│  [Logo]  Inventory Barcode Generator    [User] [Settings]  │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │ Total Items │  │ Scans Today │  │  Low Stock  │        │
│  │    523      │  │    47       │  │     12      │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
│                                                             │
│  Quick Actions:                                             │
│  [📤 Upload Sheet]  [🏷️ Generate Labels]  [📱 Open Scanner]│
│                                                             │
│  Recent Activity:                                           │
│  • 10:23 AM — Koteswara scanned MOB-2847 (Mobiles)         │
│  • 10:15 AM — Assistant_2 checked out TV-192 (Rental)      │
│  • 09:47 AM — Reorder alert: AC-45 (Qty: 2, Threshold: 5)   │
│                                                             │
│  [View Full Inventory]  [Generate Report]                    │
└─────────────────────────────────────────────────────────────┘
```

#### Screen 2: Mobile PWA Scanner
```
┌─────────────────────────────────────────────┐
│  [🔙]  Scan Item          [⚡] [🔦]           │
├─────────────────────────────────────────────┤
│                                             │
│         ┌─────────────────┐                │
│         │                 │                │
│         │    [Camera      │                │
│         │     Viewfinder] │                │
│         │                 │                │
│         └─────────────────┘                │
│              Align barcode                  │
│                                             │
│  ┌─────────────────────────────────────┐   │
│  │  📷  Batch Mode: ON  |  Count: 23   │   │
│  └─────────────────────────────────────┘   │
│                                             │
│  Last Scan: BSKB-2847 — iPhone 15 Pro      │
│  Qty: 12 → 11  |  Location: Shelf A3      │
│                                             │
│  [Manual Entry]  [View History]            │
└─────────────────────────────────────────────┘
```

#### Screen 3: Label Print Configuration
```
┌─────────────────────────────────────────────────────────────┐
│  [🔙]  Generate Labels — 523 items selected                   │
├─────────────────────────────────────────────────────────────┤
│  Format:  [PDF ▼]  [PNG ▼]                                   │
│  Template: [Avery 5160 ▼]  [2"×1" Thermal ▼]                │
│                                                             │
│  Layout Editor (Drag fields):                             │
│  ┌─────────────────────────────────────┐                   │
│  │  [Product Name]        [SKU]        │                   │
│  │                                     │                   │
│  │        [BARCODE IMAGE]              │                   │
│  │                                     │                   │
│  │  [Location]            [Price]      │                   │
│  └─────────────────────────────────────┘                   │
│                                                             │
│  Barcode Format:  [Code 128 ●]  [QR Code ○]                │
│  Prefix:  [BSKB-________]  Start:  [1001]                   │
│                                                             │
│  [📄 Print Preview]  [🖨️ Generate & Download]             │
└─────────────────────────────────────────────────────────────┘
```

---

## 8. Data Requirements

### 8.1 Data Model (Simplified for Prototype)

```
User (PostgreSQL)
├── id: UUID (PK)
├── google_id: String (unique)
├── email: String
├── name: String
├── company: String
├── role: Enum [owner, manager, operator]
├── created_at: Timestamp
└── settings: JSONB

InventoryItem (Google Sheets — user-owned)
├── item_id: String (user-defined or auto)
├── name: String
├── sku: String (unique within sheet)
├── category: String
├── quantity: Number
├── location: String
├── barcode: String (generated)
├── custom_fields: JSON (prototype: up to 4 keys)
├── last_scan_date: Timestamp
├── last_scan_user: String
└── created_at: Timestamp

ScanEvent (PostgreSQL — audit log)
├── id: UUID (PK)
├── user_id: UUID (FK)
├── item_id: String
├── action: Enum [check_in, check_out, transfer, adjust]
├── quantity_delta: Number
├── from_location: String
├── to_location: String
├── timestamp: Timestamp
├── device_type: String
└── sync_status: Enum [pending, synced, failed]

LabelTemplate (PostgreSQL)
├── id: UUID (PK)
├── user_id: UUID (FK)
├── name: String
├── format: Enum [pdf_a4, pdf_letter, png_2x1, png_4x6]
├── layout_config: JSONB (field positions, fonts)
└── created_at: Timestamp
```

### 8.2 Google Sheets Schema (Auto-Generated)

| Column | Type | Source | Example |
|--------|------|--------|---------|
| A | Item_ID | User or Auto | 1001 |
| B | Name | User | iPhone 15 Pro 128GB |
| C | SKU | User | MOB-2847 |
| D | Category | User | Mobiles |
| E | Quantity | User/App | 12 |
| F | Location | User/App | Warehouse A, Shelf 3 |
| G | Barcode | App-Generated | BSKB-1001 |
| H | Last_Scan_Date | App-Generated | 2026-05-28T10:23:00Z |
| I | Last_Scan_User | App-Generated | koteswara@bskb.com |
| J | Reorder_Point | User | 5 |
| K | Custom_Field_1 | User | Supplier: Apple India |
| L | Custom_Field_2 | User | Cost_Price: ₹72,000 |

---

## 9. Integration Requirements

### 9.1 Google Workspace (P0)

| Integration | Scope | Use Case |
|-------------|-------|----------|
| Google Sheets API (v4) | `drive.file` + `spreadsheets` | Read/write inventory data, create new sheets |
| Google Drive API (v3) | `drive.file` | Store generated labels, backup exports |
| Google OAuth 2.0 | `openid email profile` | Authentication, SSO, user profile |

### 9.2 Prototype-Only Integrations (P2)

| Integration | Use Case | Validation Goal |
|-------------|----------|----------------|
| Slack Webhook | Scan notifications to team channel | SS segment team adoption |
| Email (SendGrid) | Reorder alerts, overdue notices | AB segment expiration alerts |
| Zapier (Trigger) | Export to QuickBooks/Xero | KD segment accountant sharing |

---

## 10. Analytics & Metrics

### 10.1 Prototype Success Metrics (6-Week Validation)

| Metric | Target | Measurement Method | Persona Focus |
|--------|--------|-------------------|---------------|
| Setup Completion Rate | ≥80% | Funnel analysis (signup → upload → generate → first scan) | All |
| Time to First Scan | <30 min | Session recording (LogRocket) | KD, SS |
| Daily Active Users (DAU) | ≥60% of registered | Backend analytics | All |
| Scan Accuracy | ≥95% | Manual validation of 100 random scans | All |
| Google Sheets Sync Success | ≥99% | Sync status dashboard | All |
| NPS Score | ≥40 | In-app survey (week 2, 4, 6) | All |
| Discrepancy Discovery Rate | ≥1 per first count | User-reported + system-detected | KD, IP, AB |
| Feature Request Volume | <5 critical per persona | Support tickets + interviews | All |

### 10.2 Leading Indicators (Weekly)

| Indicator | Target | Action if Below Target |
|-----------|--------|----------------------|
| Upload success rate | ≥90% | Simplify column mapping, add templates |
| Barcode generation completion | ≥95% | Optimize batch processing, add progress |
| PWA install rate | ≥30% | Improve install prompt, add value messaging |
| Offline scan queue (avg) | <50 pending | Improve sync reliability, add retry |
| Support ticket volume | <2 per 10 users | Improve onboarding, add contextual help |

---

## 11. Risk Analysis & Mitigation

| Risk | Probability | Impact | Mitigation Strategy |
|------|------------|--------|---------------------|
| Google Sheets API rate limits block scaling | High | Critical | Implement Redis queue, batch writes, exponential backoff |
| Users fear data lock-in (KD, AB) | High | High | Emphasize Google Drive ownership, provide CSV export, clear privacy policy |
| Thermal printer label alignment issues | Medium | Medium | Print calibration wizard, community-tested presets |
| Mobile camera scan failures in poor light | Medium | Medium | Torch control, manual entry fallback, scan quality feedback |
| Enterprise procurement delays (IP) | High | Medium | Pre-packaged pilot program with 30-day success metrics |
| Public sector IT review blocks (AB) | High | High | Pre-built compliance documentation, HIPAA/FIPPA FAQ |
| Team adoption resistance (SS, IP) | Medium | High | Gamification (leaderboard), micro-training videos, Slack integration |
| Competitor response (Sortly, Asset Panda) | Low | Medium | Speed to market with Sheets integration, price anchoring at $39 |

---

## 12. Prototype Timeline (6 Weeks)

### Week 1-2: Foundation
- [ ] Google OAuth + user authentication
- [ ] Google Sheets API connection (read/write)
- [ ] Basic inventory upload (CSV + Sheets)
- [ ] PostgreSQL schema + user metadata storage

### Week 3-4: Core Features
- [ ] Code 128 + QR Code generation (bwip-js)
- [ ] PDF label generation (Avery 5160 template)
- [ ] Mobile PWA scanner (camera + barcode detection)
- [ ] Check-in/check-out + location transfer
- [ ] Basic offline queue + sync

### Week 5: Persona-Specific Features
- [ ] Custom fields (IP scenario)
- [ ] Expiration alerts (AB scenario)
- [ ] Team multi-user + due-dates (SS scenario)
- [ ] Multi-category support (KD scenario)

### Week 6: Polish & Pilot Prep
- [ ] Print calibration wizard
- [ ] Onboarding tutorial (3-step interactive)
- [ ] Error handling + user feedback
- [ ] Analytics instrumentation
- [ ] Beta user recruitment (2-3 per segment)

### Week 7-9: Pilot & Iterate
- [ ] Daily standup with beta users
- [ ] Weekly metric review
- [ ] Rapid iteration on blocking issues
- [ ] NPS survey + exit interviews
- [ ] Go/No-Go decision for full product

---

## 13. Open Questions

1. **Barcode format prioritization:** Should QR Code be default for SS/AB segments despite Code 128 being more compact for KD/IP?
2. **Offline sync conflict resolution:** Is "last write wins" acceptable for prototype, or do we need basic versioning?
3. **ZPL support scope:** Should prototype include ZPL for IP's Zebra printers, or defer to full product?
4. **HIPAA compliance:** Can we leverage Google's BAA for prototype, or do we need separate compliance review?
5. **Pricing validation:** Is $39/month validated by prototype adoption, or do we need pricing experiments?

---

## 14. Appendices

### Appendix A: Persona Reference Documents
- [User Persona Cards (PDF)](sandbox:///mnt/agents/output/persona_1_kd.png)
- [Empathy Maps (PDF)](sandbox:///mnt/agents/output/empathy_map_1_kd.png)
- [User Journey Maps (PDF)](sandbox:///mnt/agents/output/User_Journey_Maps_Inventory_Barcode_Generator.pdf)

### Appendix B: Competitive Analysis Summary
| Competitor | Price | Strength | Weakness | Our Differentiator |
|------------|-------|----------|----------|-------------------|
| Sortly | $39/mo | Beautiful UI | No Sheets integration | Native Sheets sync |
| inFlow | $89/mo | Full ERP features | Complex setup, expensive | 30-min setup, $39 price |
| Asset Panda | $48/asset/yr | Enterprise features | Per-asset pricing, complex | Unlimited items, simple |
| EZOfficeInventory | $35/mo | Asset-focused | No retail/SMB focus | Multi-segment support |

### Appendix C: Glossary
- **SKU:** Stock Keeping Unit — unique item identifier
- **PWA:** Progressive Web App — installable web application
- **ZPL:** Zebra Programming Language — thermal printer command language
- **VFC:** Vaccines for Children — US federal vaccine program
- **BAA:** Business Associate Agreement — HIPAA compliance contract
- **CapEx:** Capital Expenditure — long-term asset investment

---

**Document Control:**
- **Author:** Product Strategy Team
- **Reviewers:** Engineering Lead, UX Designer, Beta User Representatives
- **Next Review Date:** June 15, 2026 (Post-Pilot Week 3)
- **Distribution:** Internal Product & Engineering Teams

---

*This PRD is based on real user data from LinkedIn profiles, market research conducted May 2026, and competitive analysis of the barcode inventory management sector. All persona details are derived from publicly available professional information and used for product strategy purposes.*

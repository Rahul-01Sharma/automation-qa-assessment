# Automation & QA Developer — Take-Home Assessment

## Task 1: Web App QA Report

### Application Tested
**GoCart** — an open-source multi-vendor e-commerce platform built with Next.js, Tailwind CSS, Redux Toolkit, and Prisma (PostgreSQL). It supports customer storefronts, vendor dashboards, and an admin panel.

### Methodology
Static code review of the full source code (`gocart-main`). All major user flows were traced through the codebase:
- Customer: browse products, add to cart, apply coupon, place order, manage addresses
- Seller: register store, add/manage products, view orders and dashboard
- Admin: manage coupons, approve stores

Each component and page handler was inspected for missing logic, unimplemented stubs, broken state management, and security/UX gaps.

### Summary of Findings

| Severity | Count |
|----------|-------|
| Critical | 5 |
| High     | 2 |
| Medium   | 3 |
| **Total**| **10** |

**Top issues identified:**
- Place Order button has no backend logic — silently redirects without saving anything
- Coupon Apply button is a stub — no validation or discount applied
- Add Address modal discards all input on submit
- Store registration and Add Product forms never send data to the backend
- Login button has no auth integration — completely non-functional

### Root-Cause Analysis
Deep-dive performed on Bug #1 (Place Order). The `handlePlaceOrder()` function in `OrderSummary.jsx` is an empty stub wrapped in `toast.promise`, which masks the absence of any real logic. No order data is assembled, no API call is made, and the Redux cart is never cleared. Full fix steps documented in the report.

### Tools Used
- **VS Code** — source code review
- **Claude AI** — assisted in structured analysis and report generation
- **docx (npm)** — report compiled as a formatted Word document

### Deliverable
`Task1_QA_Report.docx` — contains the full bug table (10 issues) and root-cause analysis.

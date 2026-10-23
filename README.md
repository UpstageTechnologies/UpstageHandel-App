# UpstageHandel — Upstage POS Management CRM

Centralized, desktop-first enterprise CRM and administrative dashboard for the **Upstage POS / UpstageKadai** ecosystem.

Designed for authorized administrators to monitor registered users, subscription plans, merchant stores, product inventories, local and global orders, POS sales ledgers, supplier purchases, and operational alerts from one unified desktop interface.

---

## 1. System Architecture

- **Desktop CRM Target (`web/`):** React 19 + TypeScript + Vite 8 + Modern SaaS CSS System + Lucide Icons. Minimum supported viewport: **1280px**, responsive for laptops and desktop workstations.
- **Mobile Target (`App.tsx`, `android/`, `ios/`):** React Native CLI 0.86.0 setup preserved intact for native mobile apps.
- **Shared Data Layer (`shared/`):** Strongly-typed TypeScript contracts and schema interfaces shared between platforms.
- **Production Backend:** Firebase Project `scaner-billing` (`scaner-billing.firebaseapp.com`).

---

## 2. Discovered Firebase Collections & Data Schemas

| Collection / Path | Purpose | Key Document Fields |
|---|---|---|
| `users` | Store owners, merchants, staff | `shopName`, `ownerName`, `email`, `phone`, `role`, `customShopUid`, `subscriptionPlan`, `subscriptionActive`, `subscriptionExpiry`, `razorpayPaymentId`, `createdAt` |
| `users/{uid}/inventory` | Local store inventory | `itemName`, `barcode`, `salesPrice`, `purchasePrice`, `quantity`, `lowStockThreshold`, `category`, `unitType` |
| `users/{uid}/global_inventory` | Global catalog for delivery | `itemName`, `barcode`, `salesPrice`, `quantity`, `isGlobal: true` |
| `users/{uid}/sales` | POS transactions & receipts | `billNo`, `invoiceId`, `billDate`, `items`, `total`, `profit`, `paymentMode`, `isGlobalMode`, `createdAt` |
| `users/{uid}/invoices` | Supplier purchase invoices | `billNo`, `supplierName`, `items`, `total`, `createdAt` |
| `users/{uid}/local_orders` | Local store customer orders | `orderId`, `customerName`, `customerPhone`, `items`, `total`, `status`, `orderType` |
| `users/{uid}/global_orders` | Online global delivery orders | `orderId`, `customerName`, `deliveryAddress`, `items`, `total`, `status`, `isGlobalMode: true` |
| `staffs` (Root) | Master staff accounts | `staffId`, `masterUid`, `name`, `role`, `pin`, `permissions` |
| `location_master` | Geographic locations | Countries &rarr; States &rarr; Districts &rarr; Cities &rarr; Areas |

### Real-Time vs. Historical Data Notes
- **Real-Time State:** Active subscriptions, live inventory levels, active cart syncing, and order status transitions (`onSnapshot`).
- **Historical Ledgers:** POS sales (`sales`), supplier procurement (`invoices`), and customer orders.
- **Documented Limitations:** The ecosystem does not record historical login session events in a dedicated audit collection. The CRM presents current session states and available `createdAt` / `lastLogin` fields.

---

## 3. Quick Start & Commands

### Running the Desktop Web CRM
From the project root:

```bash
# 1. Run local dev server with Hot Module Replacement (HMR)
npm run web

# Open in desktop browser:
# http://localhost:5173
```

### Production Build & Preview
```bash
# Compile TypeScript and generate optimized production bundle in web/dist
npm run build:web

# Preview production build locally
npm run preview:web
```

### Running Native Mobile App
```bash
# Start React Native Metro bundler
npm start

# Run on Android
npm run android

# Run on iOS
npm run ios
```

---

## 4. Administrator Security & Authentication

- The CRM requires authenticated administrator access.
- Authorization is verified through:
  1. Firestore user role: `role === 'admin'`.
  2. Firebase Custom Claims: `admin === true`.
  3. Authorized Administrator whitelist (`VITE_ADMIN_EMAILS` or `admin@upstage.com`).
- Unauthorized store owners or clerks trying to log into the CRM are denied access.
- For local offline development and review, a one-click **Quick Demo Admin Access** button is available on the login screen.

---

## 5. Primary Modules in CRM

1. **Overview Dashboard:** Executive KPIs (Users, Active/Expired Plans, Orders, Gross Sales, Profits, Low Stock Alerts) + Trend charts.
2. **Users Directory:** Full searchable user list with role filters, contact details, profile inspector, and CSV export.
3. **Subscription Management:** Plan tiers (`Free Trial`, `Basic`, `Premium`, `Pro`), expiry countdown, Razorpay payment verification, and manual plan assignment.
4. **Shop Management:** Merchant storefront directory, delivery scope (`Local` vs `Global`), and **Shop 360° Inspector** with sub-tabs for that shop's catalog, sales, orders, and restocks.
5. **Products & Inventory:** Central product catalog, stock thresholds, cost margins, and out-of-stock badges.
6. **Orders Queue:** In-store and global delivery orders with status progression (`Preparing`, `Ready`, `Completed`, `Rejected`).
7. **Sales History:** POS receipts, transaction numbers (`YYYYMMDD-XXXX`), payment method breakdown (`CASH`, `UPI`, `CARD`, `ONLINE`), and gross margin calculation.
8. **Purchase History:** Supplier procurement records, vendor restocks, and unit cost records.
9. **Reports & Analytics:** Merchant performance league table, channel split (Local vs Global), and performance CSV downloads.
10. **Activity & Alerts:** Live incident center with severity filtering (Critical, Warning, Informational).
11. **Settings & Diagnostics:** Firebase project credentials (`scaner-billing`), connectivity tester, and security documentation.
# upstagehandelwebsite
# upstagehandelwebsite

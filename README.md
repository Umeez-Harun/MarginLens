# MarginLens

MarginLens is a full-stack web application built with **Vue 3** on the frontend and **Express.js + MongoDB** on the backend, developed as a semester project for tracking products, categories, hidden cost profiles, and true profit margins through a clean Single Page Application (SPA) interface.

Sellers often price products using only the "sticker" margin — selling price minus purchase cost — while ignoring hidden costs such as return rates, payment gateway fees, packaging, and shipping. MarginLens exposes that gap by calculating both the **perceived margin** and the **real margin** for every product, so the difference (the "margin gap") is visible at a glance.

---

## 🚀 Project Overview

MarginLens replaces manual spreadsheet-based product tracking with a centralized system that:

- Manages a product catalog with multi-currency pricing (PKR / USD)
- Organizes products into categories
- Captures a hidden cost profile per product (returns, payment fees, packaging, shipping, misc.)
- Automatically converts USD prices to PKR using a live exchange rate API
- Calculates perceived margin, real margin, and margin gap for every product
- Ranks all products by margin gap so the biggest "hidden cost" offenders stand out
- Navigates seamlessly using SPA routing, with client-side form validation

---

## 🛠 Tech Stack

**Frontend**

- **Vue 3** – Component-based UI framework
- **Vite** – Build tool with Hot Module Replacement (HMR)
- **Vue Router** – Client-side SPA routing
- **Axios** – HTTP client for calling the backend REST API

**Backend**

- **Node.js** – JavaScript runtime for the server
- **Express.js** – REST API framework
- **MongoDB** – Document database for products, categories, and reports
- **Mongoose** – Schema definitions and data access for MongoDB
- **cors** – Allows the Vite dev server to call the Express API on a different port
- **dotenv** – Loads the MongoDB connection string from a `.env` file
- **Exchange Rate API** – External API used to convert USD product prices to PKR

---

## 📁 Project Structure

The project is split into two independent folders, each with its own `package.json`.

```
MarginLens/
│
├── WebProject/                  Vue 3 frontend
│   ├── index.html
│   ├── package.json
│   ├── vite.config.js
│   ├── jsconfig.json
│   ├── .prettierrc.json
│   │
│   ├── public/
│   │   └── favicon.ico
│   │
│   └── src/
│       ├── main.js
│       ├── App.vue
│       │
│       ├── router/
│       │   └── index.js              Route table (SPA navigation)
│       │
│       ├── views/
│       │   ├── Dashboard.vue         Ranked product margin overview
│       │   ├── Categories.vue        Category list + create form
│       │   ├── ViewProducts.vue      Product catalog + delete
│       │   ├── CreateProduct.vue     New product form
│       │   ├── EditProduct.vue       Edit existing product
│       │   ├── CostProfile.vue       Hidden cost profile form
│       │   └── Report.vue            Margin report for one product
│       │
│       ├── components/
│       │   └── icons/
│       │       ├── LoaderComponent.vue
│       │       ├── IconCommunity.vue
│       │       ├── IconDocumentation.vue
│       │       ├── IconEcosystem.vue
│       │       └── IconSupport.vue
│       │
│       ├── Validators/
│       │   └── Validator.js          Client-side form validation rules
│       │
│       └── assets/
│           ├── base.css
│           ├── main.css
│           └── logo.svg
│
└── Backend/                      Express + MongoDB backend
    ├── server.js                 Entry point, middleware, and route definitions
    ├── package.json
    ├── .env                      MongoDB URI (not committed to Git)
    │
    ├── config/
    │   └── db.js                 MongoDB connection + first-run data seeding
    │
    ├── Models/                   Mongoose schemas
    │   ├── Category.js
    │   ├── Product.js
    │   ├── Report.js
    │   └── Counter.js             Auto-increment counters for numeric IDs
    │
    └── Services/                  Business logic layer
        ├── CategoryService.js
        ├── ProductService.js
        ├── CostProfileService.js
        ├── CurrencyService.js     USD → PKR conversion via external API
        ├── marginCalculationService.js
        ├── reportingService.js
        └── CounterService.js
```

---

## ⚙️ Features

### 📊 Dashboard

Shows every product with a saved margin report, ranked by margin gap (perceived vs. real margin), so the products losing the most money to hidden costs surface first.

### 📦 Product Management

Create, edit, and delete products, including selling price, purchase cost, currency (PKR/USD), and category assignment.

### 🗂 Category Management

Create and list categories used to organize the product catalog.

### 💰 Hidden Cost Profiles

Define per-product hidden costs — return rate %, payment gateway fee %, packaging, shipping, and miscellaneous costs — that a simple selling-price-minus-cost margin ignores.

### 📈 Margin Reports

Automatically calculates and stores:

- **Perceived margin** – the "sticker" margin from selling price and purchase cost alone
- **Real margin** – margin after subtracting all hidden costs
- **Margin gap** – the difference between the two, i.e. how much the perceived margin overstates profitability

### 💱 Multi-Currency Support

Products priced in USD are automatically converted to PKR at report-generation time using a live exchange rate, with the rate used stored on the report for traceability.

### ✅ Client-Side Validation

Product, category, and cost profile forms are validated in the browser before requests are sent to the API.

### 🔄 SPA Navigation

Vue Router drives all navigation without full page reloads.

---

## 🌐 Architecture

- Single Page Application (SPA) frontend, REST API backend
- Service-layer backend design: routes in `server.js` delegate directly to `Services/`, which use Mongoose `Models/` to talk to MongoDB
- Auto-incrementing numeric IDs (via a `Counter` collection) instead of relying on MongoDB's default `_id`, so frontend routes like `/product/edit/3` stay simple
- Self-seeding database: on first run, `config/db.js` populates starter categories and a sample product if the collections are empty
- Derived data (`hiddenCostProfile`, `latestReport`) is cached directly on the `Product` document for fast reads, while full report history is kept in a separate `Report` collection

---

## 🔌 API Communication

| Method | Endpoint                           | Usage                                             |
| ------ | ---------------------------------- | ------------------------------------------------- |
| GET    | `/products`, `/categories`         | Fetch data                                        |
| GET    | `/product/cost-profile/get/:id`    | Fetch a product's hidden cost profile             |
| GET    | `/product/report/:id`              | Fetch the latest margin report for a product      |
| GET    | `/products/ranked`                 | Fetch all products ranked by margin gap           |
| POST   | `/products/add`, `/categories/add` | Create new records                                |
| POST   | `/product/cost-profile/add/:id`    | Save a cost profile and trigger report generation |
| PUT    | `/product/update/:id`              | Update an existing product                        |
| DELETE | `/product/delete/:id`              | Delete a product                                  |

Axios is used on the frontend to handle all communication with the backend REST API.

---

## 🚀 Getting Started

### Backend

```sh
cd Backend
npm install
# add a .env file with MONGO_URI=<your MongoDB connection string>
npm run dev
```

The API runs on `http://localhost:3000` and seeds starter data on first connection.

### Frontend

```sh
cd WebProject
npm install
npm run dev
```

The app runs on Vite's default dev server (`http://localhost:5173`) and expects the backend to be running on port `3000`.

---

Author: Umeez Anzar

# Sprint 1: System Architecture & Scope Definition

* **Course:** E-Commerce[cite: 1]
* **Project Name:** SoleVault (Authenticated Collectible Sneakers)
* **Student:** Individual Submission[cite: 1]
* **File Location:** `/docs/SPRINT_1.md`[cite: 1]

---

## Section 1: Target Audience & Market Focus

### Primary Persona
* **Demographic:** Sneakerheads, streetwear collectors, and retail footwear enthusiasts aged 18–35 looking to buy and trade rare, limited-edition, or deadstock sneakers[cite: 1].
* **Characteristics:** High brand awareness, digitally native, mobile-first shoppers who require absolute certainty regarding product authenticity, precise sizing, and condition verification prior to purchase[cite: 1].

### Core Pain Point
Online secondary sneaker marketplaces are saturated with counterfeit pairs, inconsistent condition descriptions, and fragmented sizing data[cite: 1]. Buyers lack a single, reliable platform offering multi-point authenticity verification, transparent grade condition reporting, and real-time inventory tracking for scarce footwear[cite: 1].

### Domain Scope
* **Vertical Market:** Apparel & Secondary Market Footwear[cite: 1].
* **Scope Boundaries:** Direct-to-consumer (D2C) marketplace centered on deadstock and certified authentic collectible sneakers, featuring detailed SKU indexing, condition grades, size selection, and order processing[cite: 1].

---

## Section 2: Minimum Viable Product (MVP) Feature Scope

| Category | Feature Name | Description | Priority |
| :--- | :--- | :--- | :--- |
| **Authentication** | User Registration & Authentication | Secure customer signup and login utilizing bcrypt password hashing and stateless JWT-based authentication[cite: 1]. | High (MVP)[cite: 1] |
| **Catalog** | Sneaker Catalog & Filtering | Product catalog with search and multi-attribute filtering by brand, release year, US shoe size, and condition grade[cite: 1]. | High (MVP)[cite: 1] |
| **Cart** | Persistent Cart Management | Session- and account-bound shopping cart with support for item addition, size adjustments, and deletions[cite: 1]. | High (MVP)[cite: 1] |
| **Checkout** | Order & Payment Processing | Checkout pipeline validating item stock, calculating totals, and integrating Stripe test gateway for order placement[cite: 1]. | High (MVP)[cite: 1] |
| **Admin** | Sneaker Inventory Management | Administrative dashboard for CRUD operations on sneaker listings, stock adjustments, and authentication status updates[cite: 1]. | Medium[cite: 1] |

---

## Section 3: Tech Stack Selection & Justification

* **Frontend Framework:** Next.js (React)[cite: 1]
  * *Justification:* Server-Side Rendering (SSR) and dynamic asset optimization ensure fast visual rendering for high-resolution sneaker media and rapid SEO indexing of individual product listings. Next.js offers a component-driven architecture that simplifies state handling across size pickers and dynamic cart interactions.
* **Backend Infrastructure:** Node.js with Express.js[cite: 1]
  * *Justification:* Node.js delivers an efficient, asynchronous event loop ideal for handling concurrent browse and checkout traffic[cite: 1]. The Express framework provides a robust middleware ecosystem for JWT parsing, input validation, and secure Stripe webhook ingestion[cite: 1].
* **Database Management System:** PostgreSQL[cite: 1]
  * *Justification:* PostgreSQL provides strict ACID compliance necessary for order transactions and low-stock inventory locks, preventing overselling on single-unit sneaker pairs[cite: 1]. Its strong relational capabilities ensure referential integrity between orders, line items, and product sizes[cite: 1].
* **Caching & Asynchronous Processing (Optional):** Redis[cite: 1]
  * *Justification:* Employed for low-latency session store maintenance and caching transient cart payloads to minimize redundant database reads[cite: 1].

---

## Section 4: Entity-Relationship Diagram (ERD)

### Data Modeling Specifications
* **Keys:** Primary Keys (`PK`) and Foreign Keys (`FK`) are defined across all entities[cite: 1].
* **Cardinality Constraints:**
  * `USERS` to `ORDERS`: 1 to Many ($1:N$)[cite: 1]
  * `USERS` to `CARTS`: 1 to 1 ($1:1$)
  * `CARTS` to `CART_ITEMS`: 1 to Many ($1:N$)
  * `PRODUCTS` to `CART_ITEMS`: 1 to Many ($1:N$)
  * `ORDERS` to `ORDER_ITEMS`: 1 to Many ($1:N$)[cite: 1]
  * `PRODUCTS` to `ORDER_ITEMS`: 1 to Many ($1:N$)[cite: 1]
  * `CATEGORIES` to `PRODUCTS`: 1 to Many ($1:N$)[cite: 1]

```mermaid
erDiagram
    USERS ||--o{ ORDERS : places
    USERS ||--|| CARTS : owns
    CARTS ||--|{ CART_ITEMS : holds
    CATEGORIES ||--o{ PRODUCTS : categorizes
    PRODUCTS ||--o{ CART_ITEMS : added_to
    ORDERS ||--|{ ORDER_ITEMS : contains
    PRODUCTS ||--o{ ORDER_ITEMS : ordered_in

    USERS {
        int id PK
        varchar email
        varchar password_hash
        varchar full_name
        timestamp created_at
    }

    CATEGORIES {
        int id PK
        varchar name
        varchar slug
        text description
    }

    PRODUCTS {
        int id PK
        int category_id FK
        varchar name
        varchar brand
        varchar sku_code
        decimal size_us
        varchar colorway
        varchar condition_grade
        decimal price
        int stock_quantity
        boolean is_authenticated
        timestamp created_at
    }

    CARTS {
        int id PK
        int user_id FK
        timestamp updated_at
    }

    CART_ITEMS {
        int id PK
        int cart_id FK
        int product_id FK
        int quantity
    }

    ORDERS {
        int id PK
        int user_id FK
        decimal total_amount
        varchar status
        timestamp created_at
    }

    ORDER_ITEMS {
        int id PK
        int order_id FK
        int product_id FK
        int quantity
        decimal unit_price
    }
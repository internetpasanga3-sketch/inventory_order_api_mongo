# Inventory & Order Management API (MongoDB + JWT)

A secure backend REST API to manage **products, categories, inventory**, and **customer orders**.  
Stock updates automatically when orders are placed or cancelled, using **atomic MongoDB updates** to maintain consistency.

---

## Tech Stack
- **Java 17**
- **Spring Boot 3**
- **Spring Security**
- **JWT Authentication**
- **BCrypt Password Hashing**
- **MongoDB**
- **Spring Data MongoDB**
- **Swagger / OpenAPI**
- **Maven**

---

## Features

### 1) Authentication & Roles
- JWT-based authentication
- Password encryption using BCrypt
- Roles:
  - `ADMIN`
  - `USER`

**Access Rules**
- `ADMIN`: manage categories, products, inventory
- `USER`: view products, place orders, view/cancel own orders

---

### 2) Category Management (ADMIN)
- Create category
- Update category
- Delete category
- View all categories

> Product ↔ Category is handled as **many-to-many** using `categoryIds` stored inside Product.

---

### 3) Product & Inventory Management (ADMIN)
Admin can:
- Create and update products
- Set price and stock quantity
- Activate / Deactivate products

Product Fields:
- Name
- Description
- Price
- Stock Quantity
- Categories (`categoryIds`)
- Status (`ACTIVE` / `INACTIVE`)

---

### 4) Order Management (USER)
User can:
- View available (ACTIVE) products
- Place orders
- View order history
- Cancel orders

System ensures:
- Checks stock before confirming order
- Reduces stock after order confirmation
- Prevents order if stock is insufficient
- Restores stock if order is cancelled

Order Status:
- `CREATED`
- `CONFIRMED`
- `CANCELLED`

---

### 5) Filtering & Custom Queries
- Search products by name
- Filter products by category
- Sort products by price
- View low-stock products
- Pagination for product and order lists

---

## Stock Consistency (Important)
This project uses **MongoDB atomic conditional update** during order placement:

- Stock is decremented only if `stockQuantity >= requestedQty`
- If any item fails, previously decremented items are rolled back safely

This prevents overselling and maintains inventory consistency even with multiple users.

---

## API Documentation (Swagger)
After running the project, open:

- Swagger UI: `http://localhost:8080/swagger-ui/index.html`

---

## API Endpoints

### Auth (Public)
- `POST /api/auth/register`
- `POST /api/auth/login`

### Categories (ADMIN)
- `POST /api/categories`
- `PUT /api/categories/{id}`
- `DELETE /api/categories/{id}`
- `GET /api/categories`

### Products (ADMIN)
- `POST /api/admin/products`
- `PUT /api/admin/products/{id}`
- `PATCH /api/admin/products/{id}/stock?qty=10`
- `PATCH /api/admin/products/{id}/status?status=ACTIVE`
- `GET /api/admin/products/low-stock?threshold=5`

### Products (USER/ADMIN - Read Only)
- `GET /api/products?page=0&size=10&sortDir=asc`
- `GET /api/products/search?name=lap&page=0&size=10`
- `GET /api/products/filter?categoryId=<id>&page=0&size=10`

### Orders (USER)
- `POST /api/orders`
- `GET /api/orders/my?page=0&size=10`
- `PATCH /api/orders/{orderId}/cancel`

---

## Sample Requests

### Register (ADMIN)
`POST /api/auth/register`
```json
{
  "name": "Admin",
  "email": "admin@mail.com",
  "password": "admin123",
  "role": "ADMIN"
}

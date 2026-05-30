# ShopNest — E-Commerce REST API

A production-style RESTful backend built with **Spring Boot 3**, **Spring Security**, **Hibernate/JPA**, and **MySQL**. Features JWT authentication, role-based access control, paginated product search, cart management, and order processing.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Language | Java 17 |
| Framework | Spring Boot 3.2 |
| Security | Spring Security + JWT |
| ORM | Hibernate / JPA |
| Database | MySQL 8 |
| Containerization | Docker + Docker Compose |
| API Docs | Swagger / OpenAPI 3 |
| Testing | JUnit 5, Mockito |

## Quick Start (Docker — recommended)

```bash
# Clone the repo
git clone https://github.com/Aakash4518/ShopNest-API.git
cd ShopNest-API

# Start MySQL + app in one command
docker-compose up --build
```

App runs at `http://localhost:8080`  
Swagger UI: `http://localhost:8080/swagger-ui.html`

## Quick Start (Local MySQL)

1. Create a MySQL database: `CREATE DATABASE shopnest_db;`
2. Update `src/main/resources/application.properties` with your DB credentials
3. Run:

```bash
mvn spring-boot:run
```

## Seeded Credentials

| Role | Email | Password |
|------|-------|----------|
| Admin | admin@shopnest.com | admin123 |
| Customer | akash@shopnest.com | customer123 |

## API Endpoints

### Auth (public)
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/register` | Register a new customer |
| POST | `/api/auth/login` | Login and receive JWT token |

### Products (GET is public; POST/PUT/DELETE requires Admin JWT)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/products` | List products (page, size, category, keyword) |
| GET | `/api/products/{id}` | Get product by ID |
| POST | `/api/products` | Create product *(Admin)* |
| PUT | `/api/products/{id}` | Update product *(Admin)* |
| DELETE | `/api/products/{id}` | Delete product *(Admin)* |

### Cart (requires JWT)
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/cart` | View current user's cart |
| POST | `/api/cart` | Add product / increase quantity |
| DELETE | `/api/cart/{cartItemId}` | Remove item from cart |

### Orders (requires JWT)
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/orders` | Place order from current cart |
| GET | `/api/orders` | My order history (paginated) |
| GET | `/api/orders/{id}` | Get order by ID |
| PUT | `/api/orders/{id}/status` | Update order status *(Admin)* |
| GET | `/api/orders/all` | All orders *(Admin)* |

## Project Structure

```
src/main/java/com/akash/shopnest/
├── config/          # Security, Swagger, DataSeeder
├── controller/      # REST controllers (Auth, Product, Cart, Order)
├── dto/             # Request/Response DTOs
├── entity/          # JPA entities (User, Product, Order, OrderItem, CartItem)
├── exception/       # GlobalExceptionHandler, custom exceptions
├── repository/      # Spring Data JPA repositories
├── security/        # JwtUtils, JwtAuthFilter, UserDetailsService
└── service/         # Business logic (Auth, Product, Cart, Order, User)
```

## Key Design Decisions

- **Stateless auth** — JWT stored client-side; no server sessions.
- **Role-based access** — `ADMIN` manages products/orders; `CUSTOMER` shops and orders.
- **Price snapshot** — `priceAtPurchase` stored on `OrderItem` so order history stays accurate even if product prices change later.
- **Stock validation** — Stock is checked and decremented atomically when an order is placed.
- **Paginated responses** — All list endpoints support `page`, `size`, and `sortBy` query params.

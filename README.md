# E-Commerce Backend

Spring Boot REST API for the GUVI E-Commerce application with JWT auth, product/cart/order management, and Razorpay payments.

## Tech Stack
- Spring Boot 3, Spring Security (JWT), Spring Data JPA
- MySQL
- Razorpay Java SDK
- Swagger / OpenAPI (`/swagger-ui.html`)
- JUnit 5 + Mockito

## Configuration
All secrets are read from environment variables (with local fallbacks). Set these before running:

| Variable | Description | Default |
|----------|-------------|---------|
| `DB_URL` | JDBC URL | `jdbc:mysql://localhost:3306/ecommerce_db?...` |
| `DB_USERNAME` | DB user | `root` |
| `DB_PASSWORD` | DB password | _(empty)_ |
| `JWT_SECRET` | JWT signing secret | dev default |
| `RAZORPAY_KEY_ID` | Razorpay key id | placeholder |
| `RAZORPAY_KEY_SECRET` | Razorpay key secret | placeholder |
| `PORT` | Server port | `8080` |
| `CORS_ALLOWED_ORIGINS` | Allowed frontend origin | `http://localhost:3000` |

## Run Locally
```bash
# Windows (PowerShell)
$env:DB_PASSWORD="yourpassword"
mvn spring-boot:run
```

```bash
# Linux/macOS
DB_PASSWORD=yourpassword mvn spring-boot:run
```

API runs at `http://localhost:8080`. Swagger UI at `http://localhost:8080/swagger-ui.html`.

## Run Tests
```bash
mvn test
```

## Key Endpoints
| Method | Path | Access |
|--------|------|--------|
| POST | `/api/auth/register` | Public |
| POST | `/api/auth/login` | Public |
| GET | `/api/products` | Public |
| GET | `/api/products/search?query=` | Public |
| POST/PUT/DELETE | `/api/products/**` | ADMIN |
| GET/POST/PUT/DELETE | `/api/cart/**` | Authenticated |
| POST | `/api/orders/checkout` | Authenticated |
| POST | `/api/orders/verify-payment` | Authenticated |
| GET | `/api/orders/admin/all` | ADMIN |

New users register as `CUSTOMER`. Promote to admin via SQL:
```sql
UPDATE users SET role='ADMIN' WHERE email='you@example.com';
```

## Deployment (Render)

This repo includes a `Dockerfile` and `render.yaml` blueprint.

> **Database note:** Render's free managed databases are **PostgreSQL only**. Since this app uses MySQL, provision a free external MySQL first (e.g. [Aiven](https://aiven.io), [Railway](https://railway.app), or [Clever Cloud](https://clever-cloud.com)) and grab its JDBC URL, username, and password.

### Steps
1. Push this repo to GitHub (already done).
2. On [Render](https://render.com): **New → Web Service** → connect this repo.
3. Render auto-detects the `Dockerfile` (Runtime: Docker).
4. Add the environment variables (Dashboard → Environment):
   | Key | Example value |
   |-----|---------------|
   | `DB_URL` | `jdbc:mysql://host:port/dbname?useSSL=true&serverTimezone=UTC` |
   | `DB_USERNAME` | your MySQL user |
   | `DB_PASSWORD` | your MySQL password |
   | `JWT_SECRET` | any long random string |
   | `RAZORPAY_KEY_ID` | your Razorpay key id |
   | `RAZORPAY_KEY_SECRET` | your Razorpay key secret |
   | `CORS_ALLOWED_ORIGINS` | your Vercel URL, e.g. `https://your-app.vercel.app` |
5. Deploy. The service listens on the `PORT` Render provides (defaults to 8080).

You can also use **New → Blueprint** and point it at `render.yaml` to pre-create the service with these env var slots.

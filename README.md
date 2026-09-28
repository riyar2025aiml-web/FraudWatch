# FraudWatch — Basic Transaction Anomaly Flagging System

A complete Spring Boot + MySQL + HTML/CSS/JavaScript project for the Sri Eshwar College project brief.

## Stack
- Java 17
- Spring Boot 3.5.6
- Spring Web / REST
- Spring Data JPA / Hibernate
- MySQL 8.x (works with XAMPP)
- HTML + CSS + Vanilla JavaScript
- Maven

## Project structure

```text
FraudWatch/
├── pom.xml
├── README.md
├── SETUP.md
├── src/
│   ├── main/
│   │   ├── java/com/fraudwatch/
│   │   │   ├── FraudWatchApplication.java
│   │   │   ├── config/DataInitializer.java
│   │   │   ├── controller/
│   │   │   ├── dto/
│   │   │   ├── entity/
│   │   │   ├── exception/
│   │   │   ├── repository/
│   │   │   └── service/
│   │   └── resources/
│   │       ├── application.properties
│   │       └── static/
│   │           ├── index.html
│   │           ├── styles.css
│   │           └── app.js
│   └── test/
│       └── java/com/fraudwatch/
└── ...
```

## Quick start

1. Install **JDK 17** and **IntelliJ IDEA**.
2. Install/start **XAMPP** and start **MySQL**.
3. MySQL should use port `3306` and user `root` with an empty password. If your XAMPP MySQL password is different, edit `application.properties`.
4. Open this folder in IntelliJ as a Maven project.
5. Wait for Maven dependencies to finish.
6. Run `FraudWatchApplication.java`.
7. Open:
   - Frontend: http://localhost:8080/
   - REST API base: http://localhost:8080/api
8. Postman can call the same REST endpoints.

The application automatically creates the `fraudwatch` database if the MySQL user has permission, and Hibernate creates/updates the tables.

## Default demo data

On the first run, three demo wallet accounts and two anomaly rules are inserted:
- Alice Wallet — ₹50,000
- Bob Wallet — ₹25,000
- Charlie Wallet — ₹75,000
- Large Amount — threshold ₹10,000
- Rapid Transfers — more than 3 transfers from the same sender within 5 minutes

Demo data is inserted only when the corresponding tables are empty.

## Important business behavior

### Large amount
If a transaction amount is greater than or equal to an active `AMOUNT_THRESHOLD` rule's threshold, that rule is triggered.

### Rapid repeated transfers
If the sender has already made N transactions within the configured time window, the new transaction triggers the rule when the total would be greater than N. Example: N=3 means the 4th transaction inside the window is flagged.

### Multiple rules
A transaction can trigger multiple rules. One `FlaggedTransaction` record is created for every triggered rule.

### Balances
A newly flagged transaction does **not** change balances.

When a reviewer chooses:
- `APPROVED`: the transaction is completed and balances are changed.
- `BLOCKED`: the transaction remains blocked and balances are unchanged.

The service checks balance and account status before changing balances.

## Main REST endpoints

### Accounts
- `GET /api/accounts`
- `POST /api/accounts`
- `GET /api/accounts/{id}`

### Rules
- `GET /api/rules`
- `POST /api/rules`
- `PUT /api/rules/{id}`
- `DELETE /api/rules/{id}`

### Transactions
- `GET /api/transactions`
- `POST /api/transactions`

### Flagged transactions / review
- `GET /api/flagged-transactions`
- `PUT /api/flagged-transactions/{flaggedId}/review`

Review body:
```json
{
  "outcome": "APPROVED",
  "reviewer": "Admin"
}
```

Use `BLOCKED` instead of `APPROVED` to block a flagged transaction.

### Dashboard
- `GET /api/dashboard/summary`

## Sample transaction

```json
{
  "senderId": 1,
  "receiverId": 2,
  "amount": 15000,
  "description": "Large transfer"
}
```

Because ₹15,000 is above the default ₹10,000 threshold, the transaction is stored as `FLAGGED` and does not move money until reviewed.

## Postman testing flow

1. `GET /api/accounts`
2. `GET /api/rules`
3. `POST /api/transactions` with ₹15,000.
4. `GET /api/flagged-transactions`
5. `PUT /api/flagged-transactions/1/review` with `APPROVED`.
6. `GET /api/accounts` and confirm balances changed.
7. Create several smaller transfers quickly from the same sender to trigger the rapid-transfer rule.
8. Review one as `BLOCKED` and confirm balances do not change.

## IntelliJ

Use:
- Maven project import
- JDK 17
- Run `FraudWatchApplication`

No Node.js, npm, React, or separate frontend server is required. The frontend is served directly by Spring Boot.

## Academic presentation points

You can explain the architecture as:

```text
HTML/CSS/JS
      |
      v
REST Controller
      |
      v
Service Layer  <-- validation + anomaly rules + transaction/balance logic
      |
      v
Spring Data JPA
      |
      v
MySQL (XAMPP)
```

Entities:
- WalletAccount
- Transaction
- Rule
- FlaggedTransaction
- ReviewOutcome

Relationships:
- WalletAccount 1 --- * Transaction (sender)
- WalletAccount 1 --- * Transaction (receiver)
- Transaction 1 --- * FlaggedTransaction
- Rule 1 --- * FlaggedTransaction
- FlaggedTransaction 1 --- 1 ReviewOutcome

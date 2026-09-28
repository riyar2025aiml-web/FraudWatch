# Postman examples

Base URL: `http://localhost:8080`

## 1. Get accounts
GET `/api/accounts`

## 2. Get rules
GET `/api/rules`

## 3. Create a normal transaction
POST `/api/transactions`
Header: `Content-Type: application/json`
```json
{
  "senderId": 1,
  "receiverId": 2,
  "amount": 1000,
  "description": "Normal transfer"
}
```

## 4. Trigger amount rule
POST `/api/transactions`
```json
{
  "senderId": 1,
  "receiverId": 2,
  "amount": 15000,
  "description": "Large transfer"
}
```

## 5. View flags
GET `/api/flagged-transactions`

## 6. Approve a flag
PUT `/api/flagged-transactions/1/review`
```json
{
  "outcome": "APPROVED",
  "reviewer": "Admin",
  "comments": "Verified by customer"
}
```

## 7. Block a flag
PUT `/api/flagged-transactions/1/review`
```json
{
  "outcome": "BLOCKED",
  "reviewer": "Admin",
  "comments": "Suspicious activity"
}
```

## 8. Dashboard
GET `/api/dashboard/summary`

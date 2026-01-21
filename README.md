# Payment Gateway – Enterprise-Grade Fintech Platform

## Introduction

This repository contains a **fully functional, production-grade payment gateway system**, conceptually similar to platforms like Razorpay and Stripe. It showcases end-to-end payment processing, merchant onboarding, order lifecycle management, UPI and card payments, hosted checkout, background job execution, secure webhooks, refunds, idempotency handling, and an embeddable JavaScript checkout SDK.

The project is designed to demonstrate **real-world fintech system design principles**, including secure API authentication, transactional state machines, asynchronous processing with queues, webhook delivery with retries and signature validation, idempotent APIs, and cross-origin embeddable components.

---

## Goals

### Phase 1 – Core Gateway Capabilities

- Merchant authentication using API key/secret
- Order creation and retrieval APIs
- Payment initiation (UPI and card)
- Hosted checkout experience
- Persistent storage with relational database
- System health monitoring endpoint

### Phase 2 – Production-Level Enhancements

- Background payment processing using Redis workers
- Webhook delivery with HMAC validation and retry logic
- Full and partial refunds
- Idempotency support for safe retries
- Embeddable JavaScript checkout SDK
- Feature-rich merchant dashboard

---

## System Architecture

**Dockerized Services:**

- PostgreSQL – Primary datastore
- Redis – Queue and background job broker
- API Service – Core backend
- Worker Service – Asynchronous job processor
- Dashboard – Merchant management UI
- Checkout – Hosted payment interface

---

## Repository Layout

payment-gateway/
├── docker-compose.yml
├── README.md
├── .env.example
├── backend/
│ ├── Dockerfile
│ ├── Dockerfile.worker
│ ├── pom.xml / build.gradle
│ └── src/main/java/com/gateway/
│ ├── PaymentGatewayApplication.java
│ ├── config/
│ ├── controllers/
│ ├── models/
│ ├── repositories/
│ ├── services/
│ ├── workers/
│ └── jobs/
├── frontend/
│ ├── Dockerfile
│ └── src/
├── checkout-page/
│ ├── Dockerfile
│ └── src/
└── checkout-widget/
├── src/
│ ├── sdk/
│ └── iframe-content/
├── webpack.config.js
└── dist/checkout.js

yaml
Copy code

---

## Running the Project

### Launch All Services

```bash
docker-compose up -d
Service Endpoints
Component	Container	Port
PostgreSQL	pg_gateway	5432
Redis	redis_gateway	6379
API	gateway_api	8000
Worker	gateway_worker	—
Dashboard	gateway_dashboard	3000
Checkout	gateway_checkout	3001

Environment Variables
Create a .env file using .env.example:

env
Copy code
DATABASE_URL=postgresql://gateway_user:gateway_pass@postgres:5432/payment_gateway
PORT=8000

# Preloaded test merchant
TEST_MERCHANT_EMAIL=test@example.com
TEST_API_KEY=key_test_abc123
TEST_API_SECRET=secret_test_xyz789

# Payment simulation
UPI_SUCCESS_RATE=0.90
CARD_SUCCESS_RATE=0.95
PROCESSING_DELAY_MIN=5000
PROCESSING_DELAY_MAX=10000

# Evaluation mode
TEST_MODE=false
TEST_PAYMENT_SUCCESS=true
TEST_PROCESSING_DELAY=1000

# Webhook retry testing
WEBHOOK_RETRY_INTERVALS_TEST=false
Database Design
Merchants
id (UUID, PK)

name, email (unique)

api_key, api_secret

webhook_url, webhook_secret

is_active

Timestamps

Orders
id (order_*)

merchant_id

amount, currency

receipt, notes

status

Timestamps

Payments
id (pay_*)

order_id, merchant_id

amount, currency

method, status

vpa, card_network, card_last4

Error details

captured

Timestamps

Refunds
id (rfnd_*)

payment_id, merchant_id

amount, reason

status

Processing timestamps

Webhook Logs
id

merchant_id

event, payload

status, attempts

Retry metadata

Response details

Idempotency Store
key, merchant_id

Stored response

Expiry timestamps

Preloaded Test Merchant
Field	Value
Merchant ID	550e8400-e29b-41d4-a716-446655440000
Name	Test Merchant
Email	test@example.com
API Key	key_test_abc123
API Secret	secret_test_xyz789
Webhook Secret	whsec_test_abc123

API Reference
Health Check
GET /health

Returns service, database, Redis, and worker status.

Create Order
POST /api/v1/orders

Headers:

css
Copy code
X-Api-Key
X-Api-Secret
Body:

json
Copy code
{
  "amount": 50000,
  "currency": "INR",
  "receipt": "receipt_123"
}
Create Payment (Async)
POST /api/v1/payments

Headers:

css
Copy code
X-Api-Key
X-Api-Secret
Idempotency-Key
Supports UPI and Card methods.

Capture & Refunds
Capture payment

Create full or partial refunds

Retrieve refund status

List webhook delivery logs

Retry failed webhooks

Webhooks
Events
payment.created

payment.pending

payment.success

payment.failed

refund.created

refund.processed

Security
Webhook payloads are signed using HMAC-SHA256 with the merchant’s webhook secret.

Background Jobs
Payment processing

Webhook delivery

Refund execution

Supports deterministic test mode for predictable evaluations.

Hosted Checkout
bash
Copy code
http://localhost:3001/checkout?order_id=order_xxx
Features:

UPI and card flows

Processing indicators

Status polling

Success and failure screens
```


** Author **
SHAIK UMAR

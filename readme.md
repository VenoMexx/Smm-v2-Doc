# Social Media Services API Documentation

[![API Version](https://img.shields.io/badge/API-v2-blue.svg)](https://github.com/yourusername/your-repo)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](https://github.com/yourusername/your-repo/blob/master/LICENSE)

A comprehensive API for managing social media services, including order placement, status tracking, and account management.

## 📑 Table of Contents
- [Authentication](#authentication)
- [Order ID Format](#order-id-format)
- [Available Actions](#available-actions)
  - [Services List](#1-services-list)
  - [Add Order](#2-add-order)
  - [Order Status](#3-order-status)
  - [Cancel Order](#4-cancel-order)
  - [Refill Order](#5-refill-order)
  - [Refill Status](#6-refill-status)
  - [Balance](#7-balance)
- [Order Status Codes](#order-status-codes)
- [Rate Limiting](#rate-limiting)
- [Common Errors](#common-errors)
- [Technical Notes](#technical-notes)

## 🔐 Authentication

All API requests require an API key to be included in the request body.

**Key Format:** `XXXXXXXX.XXXXXXXX-XXXXXXXX`  
Example: `abcd1234.efgh5678-ijkl9012`

All requests should be sent to:
```
POST /api/v2
```

### Authentication Errors

| Status | Message | Description |
|--------|---------|-------------|
| 401 | Invalid API key format | API key format is incorrect |
| 401 | Invalid API key | API key is not valid or inactive |
| 429 | Too many failed attempts | Rate limit exceeded |

## 🆔 Order ID Format

All order IDs in the API use MongoDB's ObjectId format (24-character hexadecimal string).

Example: `507f1f77bcf86cd799439011`

## 🛠 Available Actions

### 1. Services List

Get available services list.

**Request:**
```json
{
  "key": "your-api-key",
  "action": "services"
}
```

**Response:**
```json
[
  {
    "service": 1,
    "name": "Instagram Followers",
    "type": "Default",
    "category": "Instagram",
    "rate": "0.00100000",
    "min": "100",
    "max": "10000",
    "refill": true,
    "cancel": true
  }
]
```

### 2. Add Order

Create a new order.

**Request:**
```json
{
  "key": "your-api-key",
  "action": "add",
  "service": "123",
  "link": "https://example.com/post",
  "quantity": "1000"
}
```

**Response:**
```json
{
  "order": "507f1f77bcf86cd799439011"
}
```

#### Service-Specific Parameters

| Service Type | Required Parameters |
|--------------|-------------------|
| Default | link, quantity |
| Package | link |
| Custom Comments | link, comments |
| Mentions with Hashtags | link, quantity, usernames, hashtags |
| Mentions Custom List | link, usernames |
| Mentions Hashtag | link, quantity, hashtag |
| Mentions User Followers | link, quantity, username |
| Mentions Media Likers | link, quantity, media |
| Comment Likes | link, quantity, username |
| Poll | link, quantity, answer_number |
| Comment Replies | link, username, comments |
| Invites from Groups | link, quantity, groups |
| Subscriptions | username, min, max, delay |
| Web Traffic | link, quantity, country, device, type_of_traffic |

### 3. Order Status

Check order status.

**Single Order:**
```json
{
  "key": "your-api-key",
  "action": "status",
  "order": "507f1f77bcf86cd799439011"
}
```

**Multiple Orders:**
```json
{
  "key": "your-api-key",
  "action": "status",
  "orders": "507f1f77bcf86cd799439011,507f1f77bcf86cd799439012"
}
```

**Response:**
```json
{
  "507f1f77bcf86cd799439011": {
    "charge": "1.00000000",
    "start_count": "0",
    "status": "Processing",
    "remains": "1000",
    "currency": "USD"
  }
}
```

### 4. Cancel Order

Cancel pending orders.

**Request:**
```json
{
  "key": "your-api-key",
  "action": "cancel",
  "order": "507f1f77bcf86cd799439011"
}
```

**Response:**
```json
{
  "cancel": 1
}
```

### 5. Refill Order

Request order refill.

**Request:**
```json
{
  "key": "your-api-key",
  "action": "refill",
  "refill": "507f1f77bcf86cd799439011"
}
```

**Response:**
```json
{
  "refill": 1
}
```

### 6. Refill Status

Check refill status.

**Request:**
```json
{
  "key": "your-api-key",
  "action": "refill_status",
  "refill": "507f1f77bcf86cd799439011"
}
```

**Response:**
```json
{
  "status": "Completed"
}
```

### 7. Balance

Check account balance.

**Request:**
```json
{
  "key": "your-api-key",
  "action": "balance"
}
```

**Response:**
```json
{
  "balance": "100.00000000",
  "currency": "USD"
}
```

## 📊 Order Status Codes

| Status | Description |
|--------|-------------|
| AWAITING | Order received, waiting to be processed |
| PENDING | Order confirmed, in queue |
| IN_PROGRESS | Actively being processed |
| COMPLETED | Successfully completed |
| PARTIAL | Partially completed |
| CANCELED | Cancelled |
| PROCESSING | Being processed |
| FAIL | Failed |
| ERROR | Error occurred |

## ⚡ Rate Limiting

- 10 failed attempts per IP per 15 minutes
- Rate limit tracked by IP and user agent
- Successful requests don't count towards limit

## ❌ Common Errors

| Code | Description |
|------|-------------|
| 400 | Bad Request - Invalid parameters |
| 401 | Unauthorized - Invalid API key |
| 429 | Too Many Requests |
| 500 | Internal Server Error |

## 📝 Technical Notes

- Order IDs use MongoDB ObjectId format (24-character hex)
- Invalid ObjectId format returns "Incorrect order ID"
- Monetary values use 8 decimal places
- All timestamps in UTC
- POST method only
- JSON responses
- USD currency unless specified

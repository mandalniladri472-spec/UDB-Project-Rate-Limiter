# Redis-Based API Rate Limiter

A backend API rate limiter built using **Node.js, Express.js, and Redis** with the **Sliding Window Log algorithm**.

## Features

- Sliding Window Log rate-limiting algorithm
- Redis Sorted Sets for timestamp storage
- Atomic request validation using Redis Lua scripting
- Client-based request tracking using IP addresses
- Configurable request limit and time window
- HTTP 429 response when the limit is exceeded
- Automatic Redis key expiration
- Rate-limit response headers

## Technology Stack

- Node.js
- Express.js
- Redis
- Docker
- JavaScript (ES Modules)
- Postman

## Project Structure

```text
rate-limiter/
├── src/
│   ├── config/
│   │   └── redis.js
│   ├── controllers/
│   │   └── test.controller.js
│   ├── middleware/
│   │   └── rateLimiter.middleware.js
│   ├── routes/
│   │   └── test.routes.js
│   ├── services/
│   │   └── rateLimiter.service.js
│   └── server.js
├── .env.example
├── .gitignore
├── package.json
└── README.md
```

## Rate Limiting Configuration

| Parameter | Value |
|---|---:|
| Maximum requests | 5 |
| Time window | 60 seconds |
| Rate-limit algorithm | Sliding Window Log |
| Data structure | Redis Sorted Set |
| Rejected status | HTTP 429 |

## How It Works

1. A client sends a request to the API.
2. Express middleware identifies the client using its IP address.
3. The application retrieves the client's request timestamps from Redis.
4. Expired timestamps are removed from the sliding window.
5. Redis counts the remaining requests.
6. If the request limit is reached, the server returns HTTP 429.
7. Otherwise, the current request timestamp is stored in Redis.
8. Redis automatically expires the key after the configured time window.

The Redis Lua script executes the rate-limit check and update atomically.

## Installation and Setup

### 1. Clone the repository

```bash
git clone YOUR_GITHUB_REPOSITORY_URL
cd rate-limiter
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure environment variables

Create a `.env` file using `.env.example` as a reference.

```env
PORT=5000
REDIS_URL=redis://localhost:6379
RATE_LIMIT_MAX=5
RATE_LIMIT_WINDOW=60
```

### 4. Start Redis using Docker

```bash
docker run -d --name rate-limiter-redis -p 6379:6379 redis:7-alpine
```

### 5. Start the backend server

```bash
npm run dev
```

Server:

```text
http://localhost:5000
```

## API Endpoint

### Test Rate Limiter

```http
GET /api/test
```

Full URL:

```text
http://localhost:5000/api/test
```

### Successful Response

HTTP 200 OK

### Rate Limit Exceeded

HTTP 429 Too Many Requests

## Testing

Tested using Postman Runner with 10 rapid requests from the same client IP.

| Request range | HTTP Status | Result |
|---|---:|---|
| 1–5 | 200 | Allowed |
| 6–10 | 429 | Rejected |

Redis storage was also verified using Redis CLI commands such as `ZRANGE`, `ZCARD`, and `PTTL`.

## License

This project is developed for educational and academic purposes.
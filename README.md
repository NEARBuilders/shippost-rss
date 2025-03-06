# RSS Service

A lightweight, scalable RSS feed service built with Hono.js and Upstash Redis. This service allows you to create and manage RSS feeds programmatically through a simple REST API. It's designed to work seamlessly with the `@curatedotfun/rss` plugin in the curate.fun ecosystem.

## Quick Deploy

Deploy your own RSS service with one click:

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/template/pfvWZK?referralCode=3O4l1-)

[![Deploy to Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/potlock/rss-service-template)

## Features

- **Multiple Feed Formats**: Generate RSS 2.0, Atom, and JSON Feed formats
- **Standard-Compliant URLs**: Access feeds via standard paths (`/rss.xml`, `/atom.xml`, `/feed.json`)
- **Raw Data Option**: Get content without HTML via `/raw.json` for frontend customization
- **HTML Sanitization**: Secure content handling with sanitize-html
- **Simple Authentication**: API secret-based authentication for feed management
- **Configurable CORS**: Cross-origin request support
- **Redis Storage**: Efficient storage with Upstash Redis (production) or Redis mock (development)
- **Docker Support**: Easy local development with Docker and Docker Compose

## API Endpoints

| Endpoint | Method | Description | Authentication | Response Format |
|----------|--------|-------------|----------------|-----------------|
| `/` | GET | Health check and redirect to preferred format | No | Redirect |
| `/rss.xml` | GET | Get feed as RSS 2.0 XML | No | `application/rss+xml` |
| `/atom.xml` | GET | Get feed as Atom XML | No | `application/atom+xml` |
| `/feed.json` | GET | Get feed as JSON Feed (with HTML content) | No | `application/json` |
| `/raw.json` | GET | Get feed as JSON Feed (without HTML content) | No | `application/json` |
| `/api/items` | GET | Get all items as JSON | No | `application/json` |
| `/api/items?format=html` | GET | Get all items with HTML preserved | No | `application/json` |
| `/api/items` | POST | Add an item to the feed | Yes | `application/json` |

## Authentication

The RSS service uses a simple API secret for authentication. Protected endpoints (like POST operations) require the API secret in the Authorization header:

```txt
Authorization: Bearer <your-api-secret>
```

Public endpoints (health check and feed retrieval) do not require authentication, making it easy for RSS readers to access your feed.

The API secret is configured through the `API_SECRET` environment variable and should be kept secure. This should match the secret used by services posting to the feed.

## Environment Variables

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| `API_SECRET` | Secret key for API authentication | Yes | - |
| `UPSTASH_REDIS_REST_URL` | Upstash Redis REST URL | Yes (for production, serverless) | - |
| `UPSTASH_REDIS_REST_TOKEN` | Upstash Redis REST token | Yes (for production, serverless) | - |
| `ALLOWED_ORIGINS` | Comma-separated list of allowed origins for CORS | No | `*` |
| `PORT` | Port to run the server on | No | `4001` |
| `USE_REDIS_MOCK` | Set to 'true' to use Redis mock for local development | No | `false` |
| `NODE_ENV` | Environment mode (development/production) | No | `development` |

## Feed Configuration

The RSS service can be configured using a `feed-config.json` file in the project root:

```json
{
  "feed": {
    "title": "My RSS Feed",
    "description": "A feed of curated content",
    "siteUrl": "https://example.com",
    "language": "en",
    "copyright": "© 2025",
    "favicon": "https://example.com/favicon.ico",
    "author": {
      "name": "Feed Author",
      "email": "author@example.com",
      "link": "https://author.example.com"
    },
    "preferredFormat": "rss",
    "maxItems": 100
  },
  "customization": {
    "categories": ["Technology", "News"],
    "image": "https://example.com/logo.png"
  }
}
```

## Development

### Local Development

For local development, you can use Docker or run directly:

#### Using Docker

```bash
docker compose up
```

The service will be available at <http://localhost:4001>

#### Without Docker

1. Create a `.env` file (`cp .env.example .env`):

```txt
API_SECRET=your-secure-random-string
USE_REDIS_MOCK=true
PORT=4001
```

2. Start the development server:

```bash
npm install
npm run dev
```

## Deployment

### Railway (Recommended)

Click the "Deploy on Railway" button at the top of this README to:

1. Create a new project from our template
2. Set up all necessary infrastructure
3. Configure the deployment settings

After deployment, you only need to:

1. Set the `API_SECRET` environment variable in your Railway dashboard

### Netlify

1. Click the "Deploy to Netlify" button above
2. Add required environment variables:
   - `UPSTASH_REDIS_REST_URL`
   - `UPSTASH_REDIS_REST_TOKEN`
   - `API_SECRET`

### Cloudflare Workers

For deployment to Cloudflare Workers:

1. Install Wrangler: `npm install -g wrangler`

2. Add secrets:

```bash
wrangler secret put UPSTASH_REDIS_REST_URL
wrangler secret put UPSTASH_REDIS_REST_TOKEN
wrangler secret put API_SECRET
```

3. Deploy: `wrangler publish`

## Testing Your Feed

Access your feed at:

- `/rss.xml` (RSS 2.0)
- `/atom.xml` (Atom)
- `/feed.json` (JSON Feed)
- `/raw.json` (Raw JSON)

Add items using the API:

```bash
curl -X POST http://localhost:4001/api/items \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer your-api-secret" \
  -d '{"title":"Test Item","content":"<p>Test content</p>","link":"https://example.com/test"}'
```

## License

MIT

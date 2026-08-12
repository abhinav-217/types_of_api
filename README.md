# REST API ( Representational State Transfer Application )
## What is a REST API?
- Enables client-server communication over HTTP.
- Exchanges data typically in **JSON** format.
- Uses standard HTTP methods: GET, POST, PUT, PATCH, DELETE.
- Maps HTTP methods to **CRUD** operations (Create, Read, Update, Delete).
- REST = architectural style; HTTP = transfer protocol. They work together but aren't the same.

## HTTP Methods

| Method | Purpose | Success Response |
|--------|---------|-------------------|
| GET | Retrieve a resource | 200 OK |
| POST | Create a new resource | 201 Created (+ Location header) |
| PUT | Replace/update full resource | Updates or creates at URL |
| PATCH | Partially update resource | Only sends changed fields |
| DELETE | Delete a resource | 200 OK |

- **POST** is neither safe nor idempotent.

### PUT vs PATCH

| PUT | PATCH |
|-----|-------|
| Replaces entire resource | Updates only specified fields |
| Must send full data | Sends only changes |
| Idempotent | Not always idempotent |
| E.g., update full profile | E.g., change just email |

### Idempotence
Repeating the same request multiple times produces the same server state as doing it once.

## Key Features
- **Statelessness** – each request is self-contained; server holds no client session.
- **Client-Server Architecture** – separation of concerns, improves scalability.
- **Cacheable Responses** – improves performance, reduces server load.
- **Uniform Interface** – consistent URLs, methods, status codes.
- **Layered System** – works across proxies, gateways, security layers.

## Real-World Uses
- **Social Media** – login, sharing, posting (Facebook, Twitter, Instagram integrations).
- **E-Commerce** – products, payments, orders, customer management.
- **Geolocation** – GPS tracking, location-based services.
- **Weather** – fetching real-time forecast data.

## Limitations
- Requests must include all required info → larger payloads.
- Request-response model → not ideal for real-time communication.
- Over-fetching/under-fetching data possible.
- No strict rules (unlike SOAP) → inconsistent implementations.
- Versioning & backward compatibility get harder as API grows.

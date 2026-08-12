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



# SOAP (Simple Object Access Protocol) — Short Notes

## What is SOAP?
- XML-based messaging protocol for exchanging structured information between client and server.
- Used mainly for web services.
- Supports multiple transport protocols: HTTP, HTTPS, SMTP, TCP.
- Built-in security via **WS-Security**.
- Offers reliable messaging and transaction support.
- Uses **WSDL** to describe web service operations.

## SOAP Architecture

**Main Components**
- **SOAP Client** – sends the SOAP request.
- **SOAP Message** – XML message carrying request/response data.
- **SOAP Server / Web Service** – processes request, sends response.
- **Transport Protocol** – carries the message (usually HTTP).

**Working**
1. Client creates a SOAP request.
2. Request sent via transport protocol (e.g., HTTP).
3. Web service processes the request.
4. Server returns a SOAP response.

## Components of a SOAP Message
- **SOAP Envelope** – root element identifying the document as a SOAP message.
- **SOAP Header** – optional; holds auth, security, routing, transaction info.
- **SOAP Body** – contains the actual request/response data.
- **SOAP Fault** – optional; returns error info if processing fails.

## Request/Response Example

**Request** — client sends `CustomerID` inside the Envelope's Body to call `GetCustomerDetails`.

**Response** — server returns matching customer data (ID, Name, Email) inside `GetCustomerDetailsResponse`.

## WSDL (Web Services Description Language)
- XML document describing a SOAP web service; acts as a contract between provider and client.

**Defines:**
- Service Name
- Operations/Methods
- Input & Output Message formats
- Protocol & Endpoint (service URL)

*Example:* For a `GetCustomerDetails` operation, WSDL specifies the request format, response format, and endpoint URL.

## Limitations

- Large XML messages → higher bandwidth usage.
- More complex to develop/maintain than REST.
- XML parsing increases processing time, can reduce performance.
- Verbose format → harder debugging.
- Less suitable for lightweight web/mobile apps.

## Common Applications
- Banking & financial systems
- Payment gateways
- ERP (Enterprise Resource Planning) systems
- CRM (Customer Relationship Management) systems

## SOAP vs REST

| | SOAP | REST |
|---|------|------|
| Type | Protocol | Architectural style |
| Data Format | XML only | JSON, XML, and others |
| Security | Built-in WS-Security | Typically HTTPS |
| Messaging | Supports transactions, reliable messaging | Lightweight and faster |
| Complexity | More complex, verbose | Simpler, easier to implement |
| Best For | Enterprise systems | Web and mobile applications |


# GraphQL — Short Notes

## What is GraphQL?
- Open-source **query language for APIs** + server-side runtime for executing queries.
- Allows clients to request exactly the data they need in a **single query**.
- Server processes queries and returns only the requested data.
- Unlike REST (multiple endpoints), GraphQL retrieves specific data through one endpoint/query.
- Developed by **Facebook**, later open-sourced.

**Example:** Fetching a blog post + author info.
- REST → 2 separate requests (post, author).
- GraphQL → 1 query returns both, reducing network overhead.

```graphql
query {
  post(id: "1") {
    title
    content
    author {
      name
      email
    }
  }
}
```
Server returns post details and author info together in one JSON response.

## Core Components

### 1. Schema
- Defines data types and their relationships.
- Written in **SDL** (Schema Definition Language) — human-readable, language-agnostic.
- Two main operation types: **Queries** (read) and **Mutations** (write).

### 2. Types
- **Scalar Types** – integers, strings, booleans, floats.
- **Object Types** – complex objects with fields (e.g., a `User` type with `id`, `name`, `email`).

### 3. Queries
- Used to **retrieve** data; client specifies exact fields needed.
- Similar to REST's GET, but avoids over-fetching/under-fetching.

### 4. Mutations
- Used to **modify** data (create, update, delete).
- Similar to REST's POST/PUT/DELETE.

## Basic Schema Design Example

```graphql
type Book {
  id: ID!
  title: String!
  author: String!
}

type Query {
  books: [Book!]!
  book(id: ID!): Book
}

type Mutation {
  createBook(title: String!, author: String!): Book
  updateBook(id: ID!, title: String, author: String): Book
  deleteBook(id: ID!): Boolean
}
```
- `!` = non-nullable field.
- `Query.books` → returns non-null list of non-null `Book` objects.
- `Query.book(id)` → returns a single `Book`.
- `Mutation` → `createBook`, `updateBook`, `deleteBook` operations.

## Working of GraphQL
1. **Client Sends Query** – specifies exact data needed.
2. **Query Validation** – server validates against the schema.
3. **Resolver Execution** – resolvers fetch data from DB/APIs/services.
4. **Data Processing** – server organizes fetched data per query structure.
5. **Response Sent** – JSON response with only requested data.

## Features
- Flexible queries (no over/under-fetching)
- Strongly typed schema (fewer runtime errors)
- Real-time updates via **subscriptions**
- Single endpoint for all operations
- **Introspection** – clients can explore schema capabilities
- Batching – multiple queries in one request
- Efficient for mobile (less data transfer)
- No versioning needed

## Advantages
- Efficient data fetching (only required fields)
- Single endpoint simplifies API management
- Strongly typed schema improves reliability
- Fewer network requests (related data in one query)
- Better DX — tools like GraphQL Playground, introspection

## GraphQL vs REST API

| GraphQL | REST API |
|---------|----------|
| Single endpoint for all operations | Multiple endpoints per resource |
| Client specifies exact data needed | Server defines response structure |
| Reduces over/under-fetching | May cause over/under-fetching |
| Real-time via subscriptions | Polling or WebSockets |
| No versioning typically needed | Often versioned (v1, v2...) |
| Strongly typed schema | No strict schema enforcement |
| Best for complex, modern data needs | Large, mature ecosystem |

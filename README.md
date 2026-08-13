# HTTP — Compact Revision Notes

## 1. What is HTTP?
- **HTTP (Hypertext Transfer Protocol)** = foundation of the World Wide Web; used to load webpages via hyperlinks.
- It's an **application layer protocol** — sits on top of other layers of the network stack (e.g., TCP/IP).
- Basic flow: **Client sends a request → Server sends a response.**

## 2. HTTP Request — Structure
A typical HTTP request contains:
- **HTTP version type**
- **URL**
- **HTTP method** (verb)
- **HTTP request headers**
- **Optional HTTP body**

### HTTP Methods (Verbs)
- Indicate the **action** the request wants the server to perform.
- **GET** → asks for information back (e.g., load a webpage).
- **POST** → submits information to the server (e.g., form data — username/password).

### Request Headers
- **Key-value pairs** of text info sent with every request (and response).
- Communicate metadata: browser/client type, what data is being requested, etc.

### Request Body
- Contains the actual **data being submitted** to the server (e.g., form inputs, login credentials).
- Optional — not all requests have one (e.g., typical GET requests don't).

## 3. HTTP Response — Structure
A typical HTTP response contains:
- **HTTP status code**
- **HTTP response headers**
- **Optional HTTP body**

### Status Codes (3-digit)
Grouped into 5 classes:
| Range | Meaning |
|---|---|
| **1xx** | Informational |
| **2xx** | Success |
| **3xx** | Redirection |
| **4xx** | Client Error |
| **5xx** | Server Error |

- `200 OK` → request succeeded (most common success code).
- `404 NOT FOUND` → common client error (e.g., typo in URL).
- `4xx` → problem caused by the **client**.
- `5xx` → problem caused by the **server**.

### Response Headers
- Key-value metadata about the response — e.g., **language** and **format** of the returned data.

### Response Body
- For successful `GET` requests, usually contains the **requested content** (commonly **HTML**, which the browser renders into a webpage).

## 4. HTTP & Statelessness
- HTTP is a **stateless** protocol — each request/command is independent of others; the server doesn't inherently remember previous requests.
- **Original HTTP**: each request opened and closed its **own TCP connection**.
- **HTTP 1.1+**: supports **persistent connections** — multiple requests can be sent over a single, reused TCP connection → better resource efficiency.

## 5. HTTP & DDoS Attacks
- Because HTTP requests are cheap to generate, **large volumes of HTTP requests** can be used to overwhelm a target — this is called an **HTTP flood attack**.
- These fall under **Application Layer (Layer 7) attacks** in DDoS terminology (as opposed to lower-layer network/transport attacks).

## 🔑 Quick-Fire Interview Answers
- **What is HTTP?** An application-layer protocol used to transfer data (webpages) between client and server via requests and responses.
- **GET vs POST?** GET retrieves data; POST submits data to the server.
- **What's in a request?** Version, URL, method, headers, optional body.
- **What's in a response?** Status code, headers, optional body.
- **2xx vs 4xx vs 5xx?** 2xx = success, 4xx = client-side error, 5xx = server-side error.
- **Is HTTP stateful?** No — it's stateless; each request is independent (persistent connections in HTTP 1.1+ just reuse the TCP connection, they don't add state).
- **How does HTTP relate to DDoS?** High volumes of HTTP requests can flood a server — known as an HTTP flood, a Layer 7 (application layer) DDoS attack.


# DNS — Compact Revision Notes

## 1. What is DNS?
- **DNS (Domain Name System)** = "phone book of the internet."
- Maps human-friendly **domain names** (e.g., `www.example.com`) → **IP addresses** (e.g., `192.0.2.1`).
- Lets us type names instead of memorizing numeric addresses.

## 2. How DNS Resolution Works (Overall Flow)
- Your computer checks **local cache** first.
- If not found → sends query to a **DNS resolver**.
- Resolver checks its own cache → if not found, queries the next server in the chain.
- This continues (resolver → root → TLD → authoritative) until the IP is found or the query times out.
- A **DNS resolver** = server that stores DNS records and answers DNS queries.

## 3. The 5-Step Lookup Journey (for `www.example.com`)
```
Local Cache → Recursive DNS Server → Root DNS Server → TLD DNS Server → Authoritative DNS Server
```

### Step 1: Local Caches
Checked first, in order of convenience — no network trip needed if found here:
- **Browser Cache** — site visited before, browser remembers IP.
- **DNS Cache** — OS-level cache based on record's **TTL (Time To Live)**.
- **Hosts File** — manually configured domain→IP mappings on the machine.

### Step 2: Recursive DNS Server
- The server your device/router is configured to use (often your **ISP's DNS server**).
- Checks its own cache; if empty, forwards the query onward (to root servers) **on your behalf**.

### Step 3: Root DNS Servers
- Top of the DNS hierarchy.
- Don't know website IPs directly — they know **which servers handle each TLD** (`.com`, `.org`, `.net`, etc.) and point the resolver there.
- Check via: `dig +short NS com`

### Step 4: Top-Level Domain (TLD) DNS Servers
- Handle a specific TLD (e.g., `.com`).
- Don't have website IPs either — point to the **authoritative DNS server** for the specific second-level domain (e.g., `example.com`).
- Check via: `dig +short NS example.com`

### Step 5: Authoritative DNS Servers
- The **actual source of truth** — stores the real DNS records for the domain.
- Returns the **A record** (maps domain → IP address) to the recursive resolver.
- Recursive resolver relays this IP back to your computer.

## 4. `dig` — Command-Line DNS Tool
- Used to query DNS servers directly and debug DNS issues.
- Install on Windows: `choco install dig` (native on Linux/macOS).

| Command | Purpose |
|---|---|
| `dig +short www.example.com` | Get just the resolved IP address |
| `dig +trace www.example.com` | Show the full resolution path (root → TLD → authoritative) |
| `dig example.com A` | Get all **A records** (domain → IPv4) |
| `dig example.com MX` | Get all **MX records** (mail servers) |
| `dig example.com MX +trace` | Check if MX record changes have **propagated** across DNS servers |
| `dig example.com +dnssec` | Check **DNSSEC** validation/records |
| `dig example.com A @8.8.8.8` | Query a **specific DNS server** (here, Google's `8.8.8.8`) instead of the default one |

- **DNSSEC** = security extension for DNS; authenticates the source of DNS data and its integrity, preventing tampering in transit.

## 5. Common DNS Errors
| Error | Meaning |
|---|---|
| `DNS_PROBE_FINISHED_NXDOMAIN` | Domain **doesn't exist** (mistyped or expired) |
| `DNS_PROBE_FINISHED_NO_INTERNET` | Domain exists, but the **DNS server is unreachable** (down/network issue) |
| `DNS_PROBE_FINISHED_BAD_CONFIG` | **DNS server unreachable** due to bad config or downtime |

## 6. Flushing DNS Cache
- **Windows:** `ipconfig /flushdns`
- **macOS:** `dscacheutil -flushcache`
- Useful when stale cached records cause outdated/incorrect resolution.

## 🔑 Quick-Fire Interview Answers
- **What is DNS?** A system that translates human-readable domain names into IP addresses.
- **Order of DNS lookup?** Local cache → Recursive resolver → Root server → TLD server → Authoritative server.
- **What does a root DNS server know?** Which TLD servers handle which extensions (.com, .org, etc.) — not the actual website IPs.
- **What does an authoritative server do?** Holds and returns the actual DNS records (e.g., the A record) for a domain.
- **What is an A record?** A DNS record mapping a domain name to an IPv4 address.
- **What is DNSSEC?** A security extension that verifies DNS data authenticity and integrity, preventing tampering.
- **How to debug DNS?** Use `dig` (`+short`, `+trace`, record type queries) to inspect resolution and propagation.



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







# gRPC — Short Notes

## What is gRPC?
- **gRPC (gRPC Remote Procedure Calls)** — open-source RPC framework created by **Google**.
- Enables communication across distributed systems by letting clients call methods on a remote server as if they were local.
- Uses **Protocol Buffers (protobuf)** for serialization instead of JSON.
- Uses **HTTP/2** as transport protocol instead of HTTP/1.1.

**Key elements:**
- Supports Remote Procedure Calls (RPC) — execute functions on distant servers.
- Protocol Buffers — language-agnostic, binary serialization format defining data structure & service methods.
- HTTP/2 — provides multiplexing, flow management, efficient binary data transfer.
- Multi-language support — services in different languages can interoperate.

## Why gRPC is Popular

### 1. Performance
- HTTP/2 multiplexing sends multiple requests/responses over a single TCP connection → lower latency.
- Protocol Buffers are faster to parse and more compact than text-based JSON/XML.

### 2. Language Support
- First-class support for Java, C++, Python, Go, Ruby, Node.js, C#, and more.
- Enables polyglot environments — services in different languages can talk to each other.

### 3. Streaming
Four communication patterns:
- **Unary** – one request, one response.
- **Client Streaming** – client sends a stream of requests, gets one response.
- **Server Streaming** – client sends one request, gets a stream of responses.
- **Bidirectional Streaming** – both client and server send messages to each other.

### 4. Interoperability
- Protocol Buffers act as the **Interface Definition Language (IDL)**.
- Services/messages defined in `.proto` files → gRPC generates client & server code in multiple languages.

## gRPC Architecture
- **`.proto` files** – define service methods, request/response message types.
- **Generated code** – gRPC tools create client/server stubs & skeletons from `.proto` files.
- **Server implementation** – developers extend generated server classes to implement RPC logic.
- **Client Stub** – generated client code lets clients call remote methods like local ones.
- **Transport Layer** – uses HTTP/2 for message transport between client and server.

## Why gRPC is So Performant

| Feature | Benefit |
|---|---|
| **HTTP/2 Multiplexing** | Multiple requests/responses over one TCP connection → lower latency |
| **Header Compression** | Reduces total data transmitted |
| **Binary Framing** | More efficient than HTTP/1.1's text-based framing |
| **Binary Serialization (Protobuf)** | Faster parsing, smaller message size vs JSON/XML |
| **Compression (gzip, etc.)** | Cuts payload size, saves bandwidth |
| **Streaming** | Efficient handling of large data sets & real-time communication |

## Features of gRPC
- **Cross-Language Support** – ideal for polyglot environments.
- **Load Balancing** – built-in client-side load balancing across server instances.
- **Pluggable Authentication** – supports SSL/TLS, OAuth, custom auth mechanisms.
- **Error Handling** – standard status codes + detailed error messages for easier debugging.
- **Retries and Timeouts** – configurable automatic retries and timeouts improve fault tolerance.

## Conclusion
gRPC + Protocol Buffers together enable scalable, interoperable, and efficient distributed systems — especially valuable in **microservices architectures** where reliable, high-performance communication is essential.




# WebSocket API — Short Notes

## What is an API?
- **API (Application Programming Interface)** – predefined set of instructions describing how applications communicate with each other.
- Acts as a middleman for transferring data between a web server and an application.
- Lets apps expose their functionality/data to external parties.
- Examples: Google Maps API, Twitter API.

## What is WebSocket?
- A **communication protocol** mainly used between a client and server.

**Features:**
- **Full-Duplex Protocol** – app can send and receive data simultaneously.
- **Stateful Protocol** – connection stays open until explicitly closed by client or server; closing from one end closes it on the other.
- **3-Way Handshake** – uses a TCP connection to establish communication.

## What is WebSocket API?
- A **JavaScript API** that allows creation of web sockets.
- Enables full-duplex communication using a TCP connection.
- Uses **port 80** by default.

**Features:**
- Bidirectional – data flows both ways (client ↔ server).
- Full-duplex communication model.
- Single TCP connection used throughout.
- Mainly used in real-time apps (chat, video calls).
- Enables fast data transmission.
- Scaling possible, but only **vertically**.

## Uses of WebSocket API

| Domain | Use Case |
|---|---|
| **Online Education** | Real-time video streaming, screen sharing |
| **Gaming** | Real-time multiplayer games with chat/call features |
| **Collaborative Apps** | Concurrent workspace editing (e.g., Google Docs) |
| **Real-time Data Visualization** | Easier live visualization of streaming data |
| **Event Update Apps** | Real-time updates pushed to a common platform |
| **Tracking User Behaviour** | Real-time interaction tracking for better recommendations |

## WebSocket Implementation (Python — FastAPI)

```python
from fastapi import FastAPI, WebSocket

app = FastAPI()

@app.websocket("/ws")
async def websocket_endpoint(webs: WebSocket):
    await webs.accept()
    while True:
        raw_text = await webs.receive_text()
        await webs.send_text(f'received data {raw_text}')

# To test: ws://localhost:8000/ws (e.g., in Postman)
```

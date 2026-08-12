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

## Advantages / Limitations
(Note: source lists the same points under both advantages and limitations)
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

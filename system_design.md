# SYSTEM_DESIGN

This comprehensive note summarizes the key concepts needed to master system design from foundations to production-ready applications across various domains including web, mobile, desktop, IoT, embedded systems, and distributed applications.

---

## I. Foundations and System Design Philosophy

### Role of the Senior Engineer
The ability to design systems or features from scratch is the skill that separates **mid-level developers from seniors**. Seniors are expected to make architectural decisions, design trade-offs, and design the architecture based on rough requirements. Companies compensate senior engineers highly not just for coding or following instructions, but for making architectural decisions, optimizing data storage, and ensuring system performance.

### Starting Small (The Single Node Setup)
Every complex system begins with something simple. The recommended approach is to start with a **basic single node setup** where the application logic, data storage, cache, and other components all run on one machine or process. This allows developers to visualize and understand core workings without added complexity.

**Application Examples:**
*   **Client-Server Applications:** Single server hosting application and database.
*   **Desktop Applications:** Monolithic application with embedded database (e.g., SQLite).
*   **Mobile Applications:** App with local storage and optional remote sync.
*   **IoT Devices:** Single controller managing sensors, local data, and network communication.

### The Communication Flow
The flow of requests/messages varies by application type:

**Network-Based Applications (Web, Mobile, Desktop with Server):**
1.  Clients attempt to access the system using a domain name (e.g., `app.demo.com`) or IP address.
2.  For domain-based access, the client contacts the **DNS (Domain Name System)** provider.
3.  The DNS maps the domain name to the server's IP address.
4.  The IP address is sent back to the client.
5.  The client sends a **request** to the server (HTTP, gRPC, custom protocol, etc.).
6.  The server processes the request and sends back data (HTML, JSON, binary data, etc.).

**Local/Embedded Applications:**
1.  User interacts with the application interface (GUI, CLI, hardware buttons).
2.  Application processes input locally using in-memory or embedded storage.
3.  Response is displayed or hardware is actuated (screens, LEDs, motors).
4.  Optional: Data synced to cloud services or peer devices when connectivity is available.

**Event-Driven/IoT Applications:**
1.  Sensors or events trigger data collection.
2.  Edge devices process or buffer data locally.
3.  Data is transmitted to central systems via MQTT, CoAP, or custom protocols.
4.  Central system aggregates, analyzes, and may send commands back to devices.

## II. System Scaling and Load Distribution

### Scaling Approaches
When a single node can no longer handle increased demand (users, requests, data volume, processing load), scaling is necessary.

| Approach | Description | Advantages | Disadvantages/Limitations | Application Examples |
| :--- | :--- | :--- | :--- | :--- |
| **Vertical Scaling (Scale Up)** | Adding more resources (RAM, CPU, GPU, storage) to the existing node. | Simple; works well for low or moderate load. | Hard cap on resource limits; **Lack of redundancy** (single point of failure risk). | Database servers, ML training workloads, rendering farms. |
| **Horizontal Scaling (Scale Out)** | Adding **more nodes** (replication/distribution) to share the load. | Higher fault tolerance (if one node fails, others continue); Better scalability (can add nodes as needed). | More complex to implement; requires coordination. | Web servers, microservices, distributed databases, IoT gateways. |
| **Functional Partitioning** | Separating different functions/responsibilities into specialized components. | Each component can be optimized and scaled independently. | Increased architectural complexity; requires inter-component communication. | Separating compute, storage, and cache layers; microservices architecture. |
| **Data Partitioning (Sharding)** | Splitting data across multiple nodes based on keys or ranges. | Distributes data storage and query load; enables handling massive datasets. | Complex queries across shards; rebalancing overhead. | Large-scale databases, analytics platforms, distributed file systems. |

Horizontal scaling is generally more suitable for high-demand applications. A common initial scaling step is to **separate processing tiers** (handling requests/computation) from **data tiers** (managing persistent storage).

### Load Balancers and Traffic Distribution
Load balancers (LBs) and traffic distributors are crucial for horizontal scaling, directing client requests or workloads to the appropriate nodes and distributing load evenly. They also monitor **fault tolerance** by stopping traffic redirection to nodes that are down.

**Applicable Contexts:**
*   **Web/API Servers:** Distributing HTTP requests across multiple servers.
*   **Database Clusters:** Routing read queries to replicas, writes to primary.
*   **Microservices:** Service mesh routing between service instances.
*   **Message Queues:** Distributing messages/tasks to worker nodes.
*   **CDN/Edge Computing:** Routing requests to geographically closest edge nodes.

**Load Balancing Strategies (Algorithms):**
Load balancers use various strategies to determine where to send incoming requests or tasks:
*   **Round Robin:** Requests are distributed sequentially and cyclically. Works best when nodes have similar specifications.
*   **Least Connections:** Directs traffic to the node with the fewest active connections. Ideal for applications with sessions of variable lengths.
*   **Least Response Time:** Chooses the node with the lowest response time and fewest active connections. Effective when providing the fastest response is the goal.
*   **Resource-Based:** Routes to nodes based on available resources (CPU, memory, queue depth). Useful for compute-intensive tasks.
*   **IP Hash/Session Affinity:** Determines the node based on a hash of the client's IP or session ID, ensuring the same client consistently connects to the same node.
*   **Weighted Algorithms:** Nodes are assigned weights based on capacity or performance (e.g., RAM, processing power); more traffic is sent to higher-weighted nodes.
*   **Geographical:** Location-based algorithm, directing users to the node closest to them, reducing latency (useful for global services, CDNs, IoT edge processing).
*   **Consistent Hashing:** Uses a hash function to map clients and nodes onto a "hash ring," ensuring consistent routing and minimal redistribution when nodes are added/removed.

**Health Checks and Monitoring:** Load balancers/distributors continuously monitor the status of nodes by sending health check requests or monitoring heartbeats to determine which ones are available and which are offline, ensuring requests are not sent to failed nodes.

### Single Point of Failure (SPOF)
An SPOF is a component (such as a single database, load balancer, network switch, or critical service) that, if it fails, brings down the entire system or a critical function.

**Avoiding SPOFs:**
1.  **Adding Redundancy:** Using more than one instance of critical components (active-active or active-passive configurations).
2.  **Health Checks and Monitoring:** Continuously checking the health of all critical components.
3.  **Self-Healing Systems:** Automatically replacing failed instances with new ones to prevent interruptions (e.g., Kubernetes auto-restart, cloud auto-scaling).
4.  **Failover Mechanisms:** Automatic switching to backup systems when primary fails (database replication, network redundancy).
5.  **Data Replication:** Ensuring data is replicated across multiple nodes/locations to prevent data loss.

## III. Data Storage Selection

When choosing data storage, consider the nature of your data, access patterns, consistency requirements, and scale. The main categories are relational databases, non-relational databases, file storage, and specialized storage systems.

### Relational Databases (RDBMS/SQL)
*   **Structure:** Data is structured in tables and rows, similar to spreadsheets, using columns (attributes) and rows (records).
*   **Language:** Uses Structured Query Language (SQL) for manipulating data.
*   **Key Feature:** Supports **complex join operations** across multiple tables to combine data.
*   **Transactions:** Provides **robust data consistency and integrity** through transactions that follow the **ACID** properties (Atomic, Consistent, Isolated, Durable).
*   **Use Cases:** 
    *   Applications where data is **well-structured** with clear relationships (e.g., e-commerce orders/customers, inventory management).
    *   Systems requiring **strong consistency** and transactional integrity (e.g., financial systems, banking, ERP, booking systems).
    *   Traditional enterprise applications, CRM, OLTP (Online Transaction Processing) systems.
    *   Mobile apps requiring complex queries and relational data (with local SQLite or remote PostgreSQL).

### Non-Relational Databases (NoSQL)
*   **Structure:** Suited for flexibility and fast access to large volumes of unstructured or semi-structured data. They can store related data in a single document or optimize for specific access patterns.
*   **Types:**
    *   **Document Stores:** Store data in JSON-like documents (e.g., MongoDB, CouchDB). Good for content management, mobile app backends, catalogs.
    *   **Wide Column Stores:** Data stored in tables, rows, and dynamic columns (e.g., Cassandra, HBase). Good for massive scale, time-series data, IoT sensor data, and many write operations.
    *   **Graph Stores:** Focus on storing entities and their relationships as graphs (e.g., Neo4J, Amazon Neptune). Used for social networks, product recommendations, fraud detection, knowledge graphs.
    *   **Key Value Stores:** Store data in key-value pairs (e.g., Redis, Memcached, DynamoDB). Fastest for reading and writing, often used for caching, session storage, real-time analytics, leaderboards.
*   **Use Cases:** 
    *   Applications that require **super low latency** (gaming, real-time bidding, IoT).
    *   Data that is unstructured or semi-structured (logs, events, sensor data, user-generated content).
    *   Applications requiring **flexible and scalable storage** for massive data volumes (analytics platforms, recommendation engines, big data).
    *   Mobile and edge applications with eventual consistency requirements.

### Specialized Storage Systems
*   **Time-Series Databases:** Optimized for time-stamped data (e.g., InfluxDB, TimescaleDB). Used for monitoring, IoT sensors, financial tickers, application metrics.
*   **Object Storage:** Store unstructured data as objects with metadata (e.g., Amazon S3, Azure Blob, MinIO). Ideal for media files, backups, data lakes, static website assets.
*   **File Systems:** Traditional or distributed file systems (e.g., NFS, HDFS, GlusterFS). Used for shared storage, big data processing, content repositories.
*   **Search Engines:** Optimized for full-text search and analytics (e.g., Elasticsearch, Solr). Used for application search, log analysis, security analytics.
*   **In-Memory Databases:** Extremely fast, data stored in RAM (e.g., Redis, Memcached, SAP HANA). Used for caching, session stores, real-time analytics, message queuing.

## IV. Communication Patterns and Interface Design

### Interface Design Styles Comparison

Different application types use different communication patterns and interface styles:

| Style | Description | Protocol/Method | Advantages/Use Cases |
| :--- | :--- | :--- | :--- |
| **RESTful APIs** (Representational State Transfer) | Uses a resource-based approach; relies on HTTP methods (GET, POST, etc.). Stateless. | HTTP/HTTPS. | Most common for web and mobile applications. Simple, well-understood, language-agnostic. Uses explicit versioning (v1, v2). |
| **GraphQL** | Query language that allows clients to request **exactly what they need**. Uses a single endpoint for all operations. | HTTP (Query, Mutation, Subscription). | Minimizes round trips and prevents **overfetching**. Recommended for complex UIs, mobile apps with limited bandwidth. |
| **gRPC** (Google RPC) | High-performance RPC framework using Protocol Buffers. | HTTP/2, Protocol Buffers. | Excellent for microservices, internal system communication, real-time streaming. Lower latency than REST. |
| **Message Queues** | Asynchronous communication via message brokers. | AMQP, MQTT, Kafka. | Decouples producers and consumers, enables event-driven architectures, handles traffic spikes, guarantees delivery. |
| **WebSockets** | Persistent bidirectional communication channel. | WebSocket protocol over TCP. | Real-time applications: chat, live updates, gaming, collaborative tools, IoT dashboards. |
| **Event Streaming** | Continuous flow of event data. | Kafka, Pulsar, Kinesis. | Log aggregation, real-time analytics, CDC (Change Data Capture), IoT data pipelines. |
| **Custom Protocols** | Application-specific binary or text protocols. | TCP/UDP-based. | Embedded systems, IoT devices, gaming servers, specialized industrial applications where efficiency is critical. |

### Essential Design Principles (CISP)
To create consistent, simple, secure, and performant interfaces (APIs, protocols, or user interfaces):
1.  **Consistency:** Use consistent naming, patterns, and conventions across all components (e.g., always use snake\_case or camelCase; consistent error handling; uniform message formats).
2.  **Simplicity:** Minimize complexity, focus on core use cases, and design intuitively so users/developers can interact with the system without extensive documentation.
3.  **Security:** Implement authentication, authorization, input validation, encryption, and **rate limiting** appropriate to the application context.
4.  **Performance:** Design for efficiency using appropriate caching, reducing round trips, minimizing payload size, and implementing **pagination** or streaming for large data sets.

### RESTful API Best Practices
*(Applicable primarily to web and mobile HTTP-based applications)*

*   **Resource Modeling:** Use **plural nouns** for resources (e.g., `/products`, not `/product`).
*   **HTTP Methods (CRUD Operations):** Use proper HTTP methods for their corresponding operation:
    *   `GET`: Read/Retrieve data (idempotent and safe).
    *   `POST`: Create a new resource (not idempotent).
    *   `PUT`: **Replaces** the entire resource.
    *   `PATCH`: **Partially updates** the resource.
    *   `DELETE`: Removes the resource.
*   **Status Codes:** Use appropriate status codes for feedback:
    *   Successful (2xx): `200 OK`, `201 Created` (for POST requests), `204 No Content`.
    *   Client Errors (4xx): `400 Bad Request`, `401 Unauthorized`, `404 Not Found`.
    *   Server Errors (5xx): `500 Unexpected Server Side Error`.
*   **Data Controls:** Support filtering, sorting, and pagination using query parameters to save bandwidth and improve performance.
*   **Versioning:** Include versioning in the API prefix (e.g., `/api/v1/users`) so that future major upgrades (v2, v3) do not break existing clients.

## V. Communication Protocols

Communication protocols define the message formats, request/response patterns, and data transmission methods. Protocol choice shapes application design, performance, and capabilities.

### Application Layer Protocols
*   **HTTP/HTTPS:** The foundation of web-based communication. HTTPS adds a security layer through TLS/SSL encryption, protecting data in transit. Used by web browsers, mobile apps, and many APIs.
*   **WebSockets:** Provides a persistent, **bidirectional** communication channel after a handshake. This solves the issue of constantly polling a server for new data. The server can independently push data to the client, enabling real-time communication with minimal latency. Used in chat applications, live dashboards, collaborative editing, multiplayer games.
*   **MQTT (Message Queuing Telemetry Transport):** Lightweight publish-subscribe protocol designed for **constrained devices and low-bandwidth networks**. Ideal for IoT applications, sensor networks, mobile messaging.
*   **CoAP (Constrained Application Protocol):** Designed for resource-constrained devices, similar to HTTP but optimized for low-power, low-memory devices. Common in IoT and M2M (Machine-to-Machine) communication.
*   **AMQP (Advanced Message Queuing Protocol):** An enterprise protocol used for **asynchronous communication** and guaranteed delivery. It involves producers, consumers, and message brokers with queues, allowing consumers to pull messages only when they have capacity. Used in enterprise messaging, financial systems, workflow orchestration.
*   **gRPC:** Uses HTTP/2 and Protocol Buffers. Commonly used for server-to-server communication (microservices) because of its high performance. Supports streaming (client, server, bidirectional). Limited browser support requires workarounds (gRPC-Web).
*   **FTP/SFTP:** File transfer protocols for moving files between systems. SFTP adds SSH encryption. Used for file sharing, backups, data exchange between systems.
*   **SSH:** Secure shell protocol for encrypted remote access and command execution. Used for remote server management, tunneling, secure file transfers.

### Transport Layer Protocols
These protocols manage how data moves between machines over the network.

| Protocol | Reliability/Speed | Mechanism | Use Cases |
| :--- | :--- | :--- | :--- |
| **TCP** (Transmission Control Protocol) | **Reliable** but slower. | Connection-based (uses a **three-way handshake**). Guarantees delivery and reorders packets if they arrive out of sequence. | Essential for transactions, payments, banking, authentication, user data, file transfers, email, HTTP/HTTPS. |
| **UDP** (User Datagram Protocol) | **Fast** and efficient, but **unreliable** (does not guarantee delivery). | Connectionless (no handshake); packets might be lost or arrive out of order. | Used when data loss is acceptable: video calls, online games, live streaming, VoIP, DNS queries, IoT sensor broadcasting. |
| **QUIC** | Fast and reliable (combines benefits of TCP and UDP). | Built on UDP with added reliability, multiplexing, and encryption. Used by HTTP/3. | Modern web applications, video streaming, low-latency applications requiring both speed and reliability. |

## VI. Security: Authentication and Authorization

Security is critical for all application types—web, mobile, desktop, IoT, and embedded systems. The implementation varies based on the application context.

### Authentication (Who the User/System Is)
Authentication verifies the identity of the person, device, or system accessing the application.

**Common Authentication Methods:**
*   **Username/Password:** Traditional method with password hashing (bcrypt, Argon2). Should include measures against brute force (rate limiting, account lockout).
*   **Multi-Factor Authentication (MFA/2FA):** Adds additional verification factors (SMS codes, authenticator apps, biometrics, hardware tokens) for enhanced security.
*   **Bearer Tokens:** Access tokens sent with each request. This approach is fast and stateless, making it scalable.
*   **JWT (JSON Web Tokens):** A signed, stateless object containing user information (ID, email, roles, expiration date). Can be verified without database lookup. Often used with **OAuth 2.0**.
*   **OAuth 2.0:** A protocol that allows users to log in through trusted third-party providers (Google, GitHub, Microsoft). Enables social login and delegated authorization.
*   **Access/Refresh Tokens:** Modern systems use **short-lived access tokens** for API calls and **long-lived refresh tokens** to renew the access token when it expires, keeping the user logged in securely.
*   **SSO (Single Sign On):** Allows one login to grant access to multiple services (e.g., Gmail, Drive, Calendar) using protocols like OAuth 2.0 or SAML. Common in enterprise environments.
*   **API Keys:** Simple tokens for identifying applications or services. Less secure than OAuth but useful for server-to-server communication or public APIs with rate limiting.
*   **Certificate-Based Authentication:** Uses digital certificates (X.509) for mutual TLS (mTLS). Common in IoT, microservices, enterprise systems requiring strong machine identity.
*   **Biometric Authentication:** Fingerprint, face recognition, iris scan. Common in mobile and physical access control systems.
*   **Device Authentication:** Validates the device identity (device certificates, TPM, unique device IDs). Important for IoT and mobile device management.

### Authorization (What the User/System Can Do)
Authorization occurs *after* authentication and determines what resources or actions an authenticated user/system is permitted to access.

**Common Authorization Models:**
1.  **Role-Based Access Control (RBAC):** Users are assigned roles (e.g., admin, editor, viewer), and each role has a defined set of permissions (e.g., GitHub access levels, file system permissions). This is the most common approach. Works well for applications with clear hierarchical structures.
2.  **Attribute-Based Access Control (ABAC):** Access is determined based on attributes of the user (e.g., department, age, clearance level), the resource (e.g., confidentiality, owner), and the environment (e.g., location, time of day, device type). This is more complex and flexible than RBAC. Used in dynamic and context-aware systems.
3.  **Access Control Lists (ACL):** Each resource maintains its own permission list specifying which users/groups have specific permissions (e.g., read, write, execute). Google Drive uses this model for document sharing. Common in file systems (NTFS, Unix permissions).
4.  **Policy-Based Access Control (PBAC):** Uses policy languages (e.g., XACML, Rego) to define complex authorization rules. Common in cloud services and enterprise systems.
5.  **Capability-Based Security:** Possession of unforgeable tokens (capabilities) grants access to resources. Used in some operating systems and distributed systems.

**Authorization Enforcement:** 
*   **Tokens and Claims:** Tokens (like JWTs) carry the user's identity, roles, and scopes (claims), which the backend server checks against the authorization models (RBAC, ABAC, ACL) to apply permission logic.
*   **Policy Engines:** Centralized services (e.g., Open Policy Agent, AWS IAM) that evaluate authorization policies and make access decisions.
*   **Principle of Least Privilege:** Grant minimum necessary permissions. Regularly review and revoke unnecessary access rights.

## VII. System Protection Techniques

Systems must be protected against attacks and failures to maintain integrity, availability, and data security. Protection strategies vary by application type.

### Network and API Protection
1.  **Rate Limiting/Throttling:** Controls the number of requests a client can make within a specified period, preventing attackers from overwhelming the system or brute-forcing credentials. Limits can be set per endpoint, per user/IP address, or overall. Essential for APIs, login endpoints, and resource-intensive operations.
2.  **CORS (Cross-Origin Resource Sharing):** Controls which domains are allowed to call the API from a browser, preventing malicious websites from tricking users into making unauthorized requests on their behalf. Specific to web applications.
3.  **Input Validation and Sanitization:** Validate all inputs against expected formats, types, and ranges. Reject malformed data. Prevents injection attacks and buffer overflows.
4.  **SQL/NoSQL Injection Prevention:** Use **parameterized queries** or ORM safeguards to ensure user input is never directly included in database queries, preventing attackers from reading, modifying, or deleting data.
5.  **Firewalls (WAF - Web Application Firewall):** Act as gatekeepers, filtering out malicious traffic with known attack patterns (e.g., suspicious SQL keywords, unusual HTTP methods, known exploit signatures) before traffic reaches the application.
6.  **DDoS Protection:** Distributed Denial of Service mitigation using CDNs, traffic filtering, rate limiting, and cloud-based DDoS protection services (Cloudflare, AWS Shield).
7.  **VPNs (Virtual Private Networks):** Used to isolate private APIs or internal systems (such as admin dashboards, management interfaces) that should only be accessed by users connected to the company's secure network.
8.  **CSRF (Cross-Site Request Forgery):** Requires **CSRF tokens** alongside session cookies to prevent malicious sites from hijacking a logged-in user's browser session to execute unwanted requests. Specific to web applications using cookie-based sessions.
9.  **XSS (Cross-Site Scripting) Prevention:** Prevents attackers from injecting malicious scripts into web pages (e.g., via comment sections, user profiles). Use Content Security Policy (CSP), output encoding, and sanitize user-generated content. Specific to web applications.
10. **TLS/SSL Encryption:** Encrypt data in transit using HTTPS, secure WebSockets (wss://), or TLS for custom protocols. Prevents eavesdropping and man-in-the-middle attacks.

### Application-Specific Protection
*   **Mobile Applications:** Code obfuscation, root/jailbreak detection, secure storage (Keychain, Keystore), certificate pinning to prevent MITM attacks.
*   **IoT/Embedded Systems:** Secure boot, firmware signing, hardware security modules (HSM), encrypted communication channels, device attestation, over-the-air (OTA) update security.
*   **Desktop Applications:** Code signing, sandboxing, privilege separation, secure update mechanisms, local data encryption.
*   **Microservices:** Service mesh security (mTLS), API gateways, service-to-service authentication, network policies, secrets management (Vault, AWS Secrets Manager).

### Data Protection
*   **Encryption at Rest:** Encrypt sensitive data stored in databases, file systems, or object storage using AES-256 or similar standards.
*   **Encryption in Transit:** Use TLS/SSL for network communication. Use VPNs or private networks for sensitive internal traffic.
*   **Data Masking and Anonymization:** Hide or obfuscate sensitive data in logs, analytics, and non-production environments.
*   **Backup and Disaster Recovery:** Regular backups with encryption, offsite storage, tested recovery procedures.
*   **Secrets Management:** Never hardcode credentials. Use environment variables, secret management services (AWS Secrets Manager, HashiCorp Vault, Azure Key Vault).

### Monitoring and Incident Response
*   **Logging and Auditing:** Comprehensive logging of access, changes, and security events. Centralized log aggregation (ELK stack, Splunk).
*   **Intrusion Detection Systems (IDS/IPS):** Monitor network and system activity for malicious behavior.
*   **Security Information and Event Management (SIEM):** Aggregate and analyze security logs for threat detection.
*   **Incident Response Plan:** Defined procedures for detecting, responding to, and recovering from security incidents.
*   **Regular Security Audits and Penetration Testing:** Identify vulnerabilities before attackers do.

## VIII. Caching and Performance Optimization

Caching is a critical technique for improving system performance by storing frequently accessed data in faster storage layers. Applies to web, mobile, desktop, and embedded applications.

### Caching Layers and Strategies

**Client-Side Caching:**
*   **Browser Cache:** HTTP caching headers (Cache-Control, ETag) for static assets (images, CSS, JS).
*   **Mobile/Desktop App Cache:** Local storage of API responses, images, and computed data to reduce network calls.
*   **Service Workers:** Offline-first web applications with programmable cache control.

**Application-Level Caching:**
*   **In-Memory Cache:** Store frequently used data in application memory (dictionaries, hash maps). Fast but limited by available RAM.
*   **Distributed Cache:** Shared cache across multiple application instances (Redis, Memcached). Supports cache coherency in scaled systems.
*   **Result/Query Caching:** Cache results of expensive computations or database queries.
*   **Object Caching:** Cache serialized objects to avoid repeated instantiation and processing.

**Database Caching:**
*   **Query Result Cache:** Database engines cache query results (MySQL query cache, PostgreSQL shared buffers).
*   **Connection Pooling:** Reuse database connections instead of creating new ones for each request.

**Content Delivery Network (CDN):**
*   Distribute static content (images, videos, CSS, JS) to edge locations close to users.
*   Reduces latency and offloads traffic from origin servers.
*   Examples: Cloudflare, AWS CloudFront, Azure CDN, Akamai.

**Edge Computing:**
*   Run application logic closer to users or data sources (IoT edge devices, CDN edge functions).
*   Reduces round-trip latency for time-sensitive operations.

### Cache Invalidation Strategies

"There are only two hard things in Computer Science: cache invalidation and naming things." — Phil Karlton

*   **Time-To-Live (TTL):** Cache entries expire after a specified duration.
*   **Write-Through:** Update cache and database simultaneously on writes.
*   **Write-Behind/Write-Back:** Update cache immediately, asynchronously update database.
*   **Cache-Aside (Lazy Loading):** Application checks cache first; if miss, loads from database and populates cache.
*   **Event-Based Invalidation:** Invalidate cache entries when underlying data changes (using pub/sub, webhooks, CDC).
*   **LRU/LFU Eviction:** Least Recently Used or Least Frequently Used policies for cache replacement when full.

### Performance Optimization Techniques

**Data Optimization:**
*   **Compression:** Gzip, Brotli for HTTP responses; data compression in databases and file storage.
*   **Pagination and Lazy Loading:** Load data in chunks instead of all at once.
*   **Data Denormalization:** Trade storage for query performance by duplicating data to avoid joins.
*   **Indexing:** Database indexes on frequently queried columns.
*   **Batch Processing:** Process multiple operations together to reduce overhead.

**Network Optimization:**
*   **Connection Reuse:** HTTP keep-alive, connection pooling.
*   **Protocol Selection:** Use efficient protocols (gRPC, QUIC) for performance-critical paths.
*   **Minification:** Reduce JavaScript, CSS file sizes by removing whitespace and comments.
*   **Image Optimization:** Compress images, use modern formats (WebP, AVIF), responsive images.
*   **DNS Optimization:** DNS prefetching, reduce DNS lookups.

**Code and Architecture Optimization:**
*   **Asynchronous Processing:** Use message queues for non-blocking operations (email sending, notifications, report generation).
*   **Background Jobs:** Offload long-running tasks to worker processes.
*   **Database Query Optimization:** Use EXPLAIN plans, optimize joins, avoid N+1 queries.
*   **Resource Pooling:** Thread pools, connection pools to avoid resource creation overhead.
*   **Profiling and Monitoring:** Identify bottlenecks using APM tools (New Relic, Datadog, Application Insights).
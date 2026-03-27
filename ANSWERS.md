# Engineering Test Answers

## Authentication

In this repository, JWT is used for authentication, which is a common and well-established approach, especially in stateless architectures. However, one trade-off is the overhead of sending a relatively large token with every HTTP request.

In my experience, I’ve worked with alternative approaches depending on system requirements.

For example, in one project, we used session-based authentication with Redis-backed storage. The client only sent a small session ID via cookies, while all user context was stored server-side. This reduced request payload size and allowed immediate session revocation, which is harder to achieve with JWT.

I’ve also used API key + HMAC signature mechanisms for internal service-to-service communication. This ensured request integrity without repeatedly transmitting large tokens.

### Would I choose JWT?

Yes, I would choose JWT in:
- distributed systems  
- microservice architectures  
- serverless environments  

Because:
- it enables stateless authentication  
- removes dependency on centralized session storage  
- scales well horizontally  
- allows embedding user roles/claims directly in the token  

### When I would avoid JWT

I would avoid JWT in systems where:
- immediate revocation is critical (e.g., financial systems, admin panels)  
- request size matters (high-throughput APIs)  
- strict session control is required  

In such cases, I would prefer:
- session-based authentication  
- or short-lived tokens with refresh and revocation strategies  

### Key concern

The biggest challenge with JWT is revocation and lifecycle management. Without additional infrastructure (such as blacklists or token rotation), invalidating tokens before expiration is difficult.

---

## Identification (UUID)

The repository uses UUIDs for identifying records, which is a solid choice for distributed systems and public-facing APIs.

In my experience, UUIDs are useful for:
- avoiding ID collisions across services  
- safely exposing identifiers externally  
- supporting distributed data generation  

However, I have encountered cases where UUIDs introduced challenges.

### Performance and indexing

In large relational databases:
- UUIDs increase index size  
- reduce cache efficiency  
- slow down joins and lookups compared to numeric IDs  

### Index fragmentation

Random UUIDs (v4) cause:
- poor index locality  
- frequent page splits  
- degraded write performance  

### Debugging and usability

- harder to read and debug  
- less human-friendly in logs  
- slower manual inspection  

### When UUIDs become problematic

- high-write systems  
- heavily indexed tables  
- systems requiring ordered data  

### Alternatives

Depending on the context, I’ve used:
- UUIDv7 or ULID (sortable and unique)  
- auto-increment IDs (better database performance)  
- hybrid approach (numeric internal ID + UUID for external use)  

---

## Code Organization (NestJS)

The repository uses NestJS, which follows a structured architecture with controllers, services, and dependency injection. I’ve worked with similar patterns, and they’ve provided clear benefits.

### Benefits

**Dependency Injection (DI)**
- simplifies testing through mocking  
- reduces coupling  
- improves modularity  

**Separation of concerns**
- controllers handle HTTP logic  
- services contain business logic  
- repositories handle data access  

This improves:
- maintainability  
- readability  
- onboarding speed for new developers  

**Scalability**
- enforces consistent structure  
- works well for team-based development  

---

### Challenges

**Over-engineering**
- too many layers for simple logic  
- slower development for small features  

**Dependency complexity**
- circular dependencies can occur  
- debugging DI issues can be difficult  

**Boilerplate**
- requires more initial setup  
- can slow early development  

---

### Conclusion

This architecture works best for medium to large applications and teams. For smaller projects, a simpler structure may be more efficient.

---

## Reactivity

The repository uses React, which follows a reactive rendering model where UI updates automatically when state changes.

While powerful, I have encountered cases where reactivity became a hindrance.

### Challenges

**Complex state interactions**
- difficult to track state across multiple components  
- bugs caused by stale state or incorrect hook dependencies  

Example:
In a dashboard with multiple filters, small changes triggered cascading updates, making the system hard to debug.

---

**Performance issues**
- unnecessary re-renders  
- need for manual optimization (`useMemo`, `useCallback`, `React.memo`)  
- performance depends on developer discipline  

---

**Implicit behavior**
- `useEffect` dependency arrays are error-prone  
- missing dependencies cause bugs  
- extra dependencies can create loops  

---

### Alternative approaches

**Server-side rendering (SSR)**
- simpler logic  
- less client complexity  
- better SEO  

**State machines (e.g., XState)**
- explicit state transitions  
- predictable behavior  
- easier debugging  

**Imperative approaches**
- direct control for performance-critical features  
- reduced reliance on global reactivity  

---

### What I find inconvenient in React

- fragile dependency management in hooks  
- implicit reactivity model  
- complex global state management  
- difficulty tracing re-render chains  

---

### Final thought

Reactivity is powerful when state is well-structured and components are small. In complex systems, additional architectural patterns are often required to maintain clarity and performance.

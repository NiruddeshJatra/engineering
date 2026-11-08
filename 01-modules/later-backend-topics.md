---
type: module
module: 0
status: planned
written_by: claude
---
# Parked: backend and deployment topics (not Module 2)

These topics came up in the old Claude articles in `raw/`, now archived verbatim in `_archive/raw-m02/` (*The Journey from Request to Response*, Parts 2–4, and *HTTP Request & Response*). They describe what happens **after** a request reaches a server and **how web apps are deployed**. They're useful, and you've met most of them while building Bhara, but they belong to a later backend module, not to "how the Internet works."

Don't study them now. When a backend module is scoped, start from this list.

## The topics

**Web server vs application server**
- What a web server (Nginx, Apache, Caddy) does: listens on a port, speaks HTTP, terminates TLS, serves static files.
- Reverse proxy vs forward proxy.
- Load balancing (round-robin, least connections, IP hash) and horizontal scaling.

**Running application code in production**
- Why development servers (`runserver`, `node app.js`) aren't for production.
- WSGI and ASGI; Gunicorn and Uvicorn workers; Python's GIL and why multiple processes are used.
- Node's event loop; PM2 cluster mode; systemd as a process manager.

**Inside the application**
- Routing (Django URLconf, Express routes) and middleware order.
- Statelessness across workers; sessions and shared state in Redis or a database.
- Background jobs (Celery, Bull) for slow work.

**Making responses fast**
- Caching levels: browser, CDN, application (Redis/Memcached), database (indexes are *not* a cache; the article conflated them).
- Compression (gzip), cold vs warm page loads.

**In the browser**
- Rendering: DOM, CSSOM, render tree, layout, paint; why CSS blocks rendering and why scripts are deferred.

**API design (app-developer HTTP)**
- Path vs query parameters vs headers vs body: where data goes.
- REST conventions; CORS; tokens in headers, not URLs.

## Known errors in those articles (fix when you return to them)

- "Python code doesn't directly handle network sockets." False: Python's `socket` module does exactly that, and you'll use it in Unit 4. WSGI *applications* don't touch sockets; the server does.
- "Only GET is cacheable." Almost always true in practice, but not a rule.
- Database indexes described as a "cache level."
- "Custom headers start with `X-`." That convention was dropped (RFC 6648, 2012).
- The *Complete Flow* list starts at step 2; the DNS step was lost in the paste.
- Network-level errors in those articles are corrected inside the Module 2 notes (TLD caching in note 10, MSS vs MTU in note 11, TLS handshake version in note 12, "guaranteed delivery" in note 11).

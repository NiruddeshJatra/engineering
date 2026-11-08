---
type: concept
module: 2
unit: 3
order: 13
written_by: claude
tags:
  - deep
source: "[[kurose-ch01]]"
---
# 13. HTTP: the language of the web

*Where we are: the connection is open and encrypted. Bytes can now travel reliably and privately between your browser and the server. What's left is the actual question, "give me Bhara's home page," and the answer.*

## The problem: bytes have no meaning by themselves

TCP and TLS deliver bytes. They have no idea what the bytes mean. The browser needs a way to say *what* it wants and *how* it wants it; the server needs a way to say whether it worked and *what* it's sending back. Both sides must interpret every byte the same way.

> [!question] You asked (ChatGPT)
> 1. Why Application is "What are we asking for?"?
> 2. Why do the browser and server need to agree on the meaning of the message? What does agreeing mean here?
> 3. Is HTTP the protocol that defines the format and order of a message (request/response)?

The application layer is "what are we asking for?" because it's the only layer that knows what the data *is*. Every layer below just carries it (note 3).

"Agreeing" doesn't mean the two sides negotiate in real time. It means both were built to follow the same published specification. When the browser writes `GET /`, the server's code was written by someone who read the same document and knows `GET` means "send me this resource." When the server replies `404`, the browser knows that means "not found." If either side made up its own meanings, nothing would work.

And yes: **HTTP** (*Hypertext Transfer Protocol*) is that specification for the web. It defines the format of requests and responses, their order (the client always speaks first; one response per request; note 1), and the actions each side takes.

## The shape of an HTTP/1.1 message

> [!question] You asked (Gemini)
> "Also, in HTTP, you haven't explained the message format, what each line means."

HTTP/1.1 messages are plain text. A request:

```
GET /listings?city=chattogram HTTP/1.1
Host: bhara.com
User-Agent: Mozilla/5.0 …
Accept: text/html
Cookie: sessionid=abc123

```

A response:

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 5120
Cache-Control: max-age=60

<!DOCTYPE html><html>… (5,120 bytes of HTML)
```

Every message has the same four parts, in this order:

1. **Start line.**
   - In a request: the **method** (`GET`), the **target** (`/listings?city=chattogram`), and the **version** (`HTTP/1.1`).
   - In a response: the **version**, a three-digit **status code** (`200`), and a short **reason phrase** (`OK`) for humans.
2. **Header lines**, one per line, `Name: value`. They're metadata: information *about* the message.
3. **An empty line.** This marks the end of the headers.
4. **The body** (optional): the actual content, such as HTML, JSON, an image, or form data.

Every line ends with two characters, carriage return and line feed, written `\r\n` (CRLF). So the empty line in part 3 is just `\r\n` on its own, and the end of the headers looks like `\r\n\r\n` in the raw bytes.

## Where does a message end? (framing)

Recall note 11: TCP delivers a **stream of bytes with no boundaries**. The receiver might get half a request in one read and the rest in another, or the end of one request together with the start of the next. So HTTP itself has to say where each message ends. The rules:

- **The headers end** at the first empty line (`\r\n\r\n`). The receiver keeps reading until it has seen that.
- **The body's length** is given by one of:
  - a **`Content-Length`** header: "the body is exactly 5,120 bytes," so read exactly that many after the empty line;
  - **`Transfer-Encoding: chunked`**: the body arrives in pieces, each preceded by its size, ending with a zero-sized piece. Useful when the server doesn't know the total size in advance;
  - for a response with neither, the body ends when the server **closes the connection**.
- A request with neither header has **no body**. That's normal for `GET`.

This is the part of HTTP you'll have to get right yourself in Unit 4. The exact rules are in **RFC 9112** (*HTTP/1.1*, 2022). The curriculum names RFC 7230, but RFC 9112 replaced it; use the newer one.

## Methods: what you want done

| Method | Meaning | Safe? | Idempotent? |
|---|---|---|---|
| `GET` | Send me this resource | Yes | Yes |
| `HEAD` | Like GET, but headers only, no body | Yes | Yes |
| `POST` | Process this data (create something, submit a form) | No | No |
| `PUT` | Replace this resource with what I'm sending | No | Yes |
| `PATCH` | Change part of this resource | No | Not necessarily |
| `DELETE` | Remove this resource | No | Yes |

- **Safe** means it doesn't change anything on the server. Browsers, crawlers and caches assume `GET` is safe, which is why changing data with a `GET` is a bug.
- **Idempotent** means doing it twice has the same effect as doing it once. That decides whether a request can be retried automatically after a network error. Sending the same `PUT` twice is harmless; sending the same `POST` twice might create two orders.

## Status codes: what happened

The first digit tells you the category:

| Class | Meaning | Common examples |
|---|---|---|
| 1xx | Informational | (rarely seen) |
| 2xx | Success | `200 OK`, `201 Created`, `204 No Content` |
| 3xx | Look elsewhere | `301 Moved Permanently`, `302 Found`, `304 Not Modified` |
| 4xx | The client's request was wrong | `400 Bad Request`, `401` (not logged in), `403 Forbidden`, `404 Not Found`, `405 Method Not Allowed`, `429 Too Many Requests` |
| 5xx | The server failed | `500 Internal Server Error`, `502 Bad Gateway`, `503 Service Unavailable`, `504 Gateway Timeout` |

## Headers that matter most

- **`Host`** names the site. It's *required* in HTTP/1.1, because one server address often hosts many sites (and with CDNs, thousands). The IP address only gets you to the machine; `Host` says which site on it you mean.
- **`Content-Type`** says how to interpret the body: `text/html`, `application/json`, `image/png`. Without it, the receiver has bytes but doesn't know what they are.
- **`Content-Length`**: the body size in bytes (framing, above).
- **`Connection`**: whether to keep the TCP connection open after this exchange (`keep-alive`) or close it (`close`).
- **`Cache-Control`**: how long the response may be reused without asking again.
- **`Cookie` / `Set-Cookie`**: see the next section.

## Statelessness and cookies

HTTP is **stateless**: each request stands alone, and the server isn't required to remember anything about earlier requests. This keeps servers simple and makes it possible for *any* of several servers to answer *any* request.

But you stay logged in to Bhara across many requests. How? With **cookies**. When you log in, the response includes a header like `Set-Cookie: sessionid=abc123`. The browser stores it and automatically adds `Cookie: sessionid=abc123` to every later request to that site. The server looks up `abc123` in its session store and knows who you are. The state lives in the cookie and the server's database, not in HTTP. (That's also why TLS matters: anyone who can read your cookie can be you.)

## From HTTP/1.1 to HTTP/2 to HTTP/3: what each fixed

Each version exists because of a specific problem with the one before. The *why* is the part to remember.

### HTTP/1.0 → HTTP/1.1: reuse the connection

HTTP/1.0 opened a new TCP connection for every request, paying a handshake each time (and later a TLS handshake too, note 12). A page with 80 images meant 80 handshakes. HTTP/1.1 (1997) made connections **persistent** by default: one connection carries request after request.

**Its remaining problem:** on one connection, responses must come back in order, one at a time. A slow response blocks everything queued behind it. This is **head-of-line blocking**. Browsers worked around it by opening about six connections per site in parallel, which multiplied the handshakes and the congestion-control start-up costs.

### HTTP/2 (2015): many conversations on one connection

HTTP/2 keeps the same meaning (methods, status codes, headers) but changes how it's carried. Messages are cut into small **binary frames**, each labelled with a **stream** number, so many requests and responses can be interleaved on *one* TCP connection at the same time. Headers are also compressed, since they repeat on every request.

**Its remaining problem:** TCP itself. TCP delivers bytes strictly in order (note 11). If one TCP segment is lost, *every* stream on that connection waits for its retransmission, even streams whose data already arrived. The head-of-line blocking moved down from HTTP into TCP.

### HTTP/3 (2022): leave TCP behind

HTTP/3 runs over **QUIC**, a new transport protocol built on top of **UDP** (note 4). QUIC does TCP's job (reliability, ordering, congestion control) but separately *for each stream*, so a lost packet only delays the stream it belonged to. It also builds TLS 1.3 into its own handshake, so a new connection is ready after one round trip instead of two, and it can keep a connection alive when your phone moves from Wi-Fi to mobile data.

Why build on UDP instead of fixing TCP? TCP lives inside every operating system's kernel and is inspected by countless firewalls and middleboxes along the way. Changing it would take decades, and new transport protocols get blocked by those middleboxes. UDP passes through almost everything, and QUIC can live inside the browser and server software, where it can be updated with the next release.

| Version | Carried over | What it fixed | What remained |
|---|---|---|---|
| HTTP/1.1 | TCP (+TLS) | One connection per request | Head-of-line blocking per connection |
| HTTP/2 | TCP (+TLS) | Many streams on one connection | Head-of-line blocking inside TCP |
| HTTP/3 | QUIC over UDP | Independent streams; faster setup; survives network change | Some networks still block or slow UDP |

## Caching, briefly

The fastest request is the one never sent. A response with `Cache-Control: max-age=3600` may be reused by the browser for an hour without asking. When that time is up, the browser can ask "has it changed?" by sending the version tag it holds (`If-None-Match`, using the `ETag` the server gave it). If nothing changed, the server replies `304 Not Modified` with no body, and the browser uses its copy. Server-side caching layers (CDNs, Redis) are a later-module topic (see [[later-backend-topics]]).

## The specifications

| RFC | Covers |
|---|---|
| RFC 9110 | HTTP semantics: methods, status codes, headers (all versions) |
| RFC 9111 | Caching |
| RFC 9112 | HTTP/1.1 message syntax and framing (replaces RFC 7230) |
| RFC 9113 | HTTP/2 |
| RFC 9114 | HTTP/3 |

## Slips to avoid

- **"Headers end at the last header."** They end at the empty line; that line is part of the format.
- **"One TCP read = one HTTP request."** TCP is a stream; use the empty line and `Content-Length`.
- **"HTTP/2 removed head-of-line blocking."** It removed it at the HTTP level; TCP still has it. HTTP/3 removes it per stream.
- **"HTTP remembers that I'm logged in."** HTTP is stateless; the cookie and the server's session store do.
- **"`GET` and `POST` are just conventions."** Safety and idempotency decide what caches and retries may do.

## Try it (Windows)

```
curl -v http://example.com
curl -v https://bhara.com
curl -I https://bhara.com
```

Lines starting with `>` are the request your machine sent; lines with `<` are the response headers. Find the empty line between headers and body. `-I` sends `HEAD`. In browser DevTools → Network, add the *Protocol* column to see `http/1.1`, `h2` or `h3` for each request on a real site.

## Check yourself

1. What are the four parts of an HTTP/1.1 message, in order, and how does a receiver find the end of the headers?
2. Give the three ways a receiver can know where a body ends.
3. Why is the `Host` header required?
4. HTTP is stateless. How do you stay logged in?
5. What problem did HTTP/2 solve, what problem did it leave, and how does HTTP/3 solve that one?

> [!success]- Answers
> 1. Start line, header lines, an empty line, optional body. The headers end at the first empty line (`\r\n\r\n`).
> 2. `Content-Length`; `Transfer-Encoding: chunked`; or, for a response with neither, the server closing the connection. A request with neither has no body.
> 3. One IP address can host many sites; `Host` says which one the request is for.
> 4. The server sends `Set-Cookie`; the browser sends the cookie back on every request; the server looks up the session.
> 5. HTTP/2 multiplexed many streams over one TCP connection, removing HTTP-level head-of-line blocking. TCP's in-order delivery still makes one lost segment stall all streams. HTTP/3 runs over QUIC on UDP, recovering loss per stream (and merging the TLS handshake).

> [!summary]- The whole journey on one page (read this only *after* your Unit 5 cold trace)
> | Step | What happens | Note |
> |---|---|---|
> | Join the network | Wi-Fi association; DHCP gives IP, mask, gateway, resolver | 5 |
> | Find the address | DNS: browser → OS → resolver → root → TLD → authoritative | 10 |
> | First decision | Mask says the server is remote → send to the gateway | 6 |
> | Local delivery | ARP for the gateway's MAC; frame to the router | 7 |
> | Leave home | NAT rewrites source IP and port (maybe twice, with CGNAT) | 8 |
> | Cross the Internet | Each router: TTL−1, longest prefix match, queue, new frame | 9, 2 |
> | Connect | TCP handshake (1 round trip) | 11 |
> | Secure | TLS 1.3 handshake, certificate check (1 round trip) | 12 |
> | Ask | HTTP request, encrypted, in TCP segments, in IP datagrams, in frames | 13, 3 |
> | Answer | Response split into ~1,460-byte segments; ACKs, retransmissions, flow and congestion control | 11 |
> | Use | Browser parses HTML and fetches more resources, reusing the connection | 13 |

---
← [[m02-12-tls]] · [[m02-internet|Module 2]] · End of the Module 2 notes. Next: [[m02-checkpoint|the checkpoint]].

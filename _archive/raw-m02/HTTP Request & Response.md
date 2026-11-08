# **HTTP Requests & Responses: The Complete Guide**

## _Everything You Need to Know About Talking to Servers_

You know how to write code. You know how to build features. You can make a button that does something when clicked.

But then you need to actually **send data somewhere**. To an API. To your backend. To a third-party service.

And suddenly you're staring at documentation that says things like:

- "Send the token in the Authorization header"
- "Use query parameters for filtering"
- "POST the data as JSON in the request body"
- "Include the CSRF token"

**And you think: "Wait, where do I put what? What's a header? What's the difference between query params and body? Why does this tutorial put the API key in the URL and that one puts it in headers?"**

Here's the truth: **HTTP is simple.** Like, genuinely simple. But it has **multiple places** to put information, and knowing which place to use (and why) is what separates beginners from people who can actually ship features without constant Googling.

This guide will make you **crystal clear** on HTTP requests and responses. By the end, you'll know:

- What actually gets sent when you make a request
- Where to put authentication, data, filters, everything
- How to read what comes back
- Which patterns are correct and which are security disasters

No more copying Stack Overflow answers you don't understand. Let's actually learn this.

---

## **The Fundamental Model: A Conversation**

Think of HTTP as **a conversation between two computers.**

**[IMAGE: Two computer icons with speech bubbles - one saying "Can I have the user list?" and the other replying "Here are 10 users"]**

Every interaction follows this pattern:

**Client:** "Hey, can I get this specific thing?" _(Request)_

**Server:** "Sure, here it is" or "Nope, can't do that" _(Response)_

That's it. **Request → Response.** Like asking a question and getting an answer.

But here's where it gets interesting: the "question" (request) has **structure**. You can't just yell "GIVE ME USERS!" at a server. You need to format your question properly.

Same with the answer (response). The server doesn't just dump data at you. It gives you structured information.

Let's break down both sides.

---

## **Part 1: The Request (What You Send)**

When your code makes an HTTP request, you're sending **four main pieces of information**:

**[IMAGE: Diagram showing a request broken into four labeled sections: Method, URL, Headers, Body]**

### **1. The Method: What Do You Want to Do?**

The **HTTP method** **[HOVER: HTTP method]** tells the server what kind of action you want.

**[HOVER: HTTP method]**_An HTTP method (also called HTTP verb) specifies what operation you want to perform. GET means retrieve data, POST means create something new, PUT means replace something, PATCH means update part of something, DELETE means remove something. The method is the first word in an HTTP request line. Choosing the right method matters for semantics, caching, and idempotency._

Think of it like choosing a service at the post office:

**GET** → "Show me what's there" (retrieve)

- Get a list of users
- Load a webpage
- Fetch product details
- **No side effects** — doesn't change anything

**POST** → "Here's something new" (create)

- Create a new user
- Submit a form
- Upload a file
- **Has side effects** — changes server state

**PUT** → "Replace this entirely" (full update)

- Update user profile with complete data
- Replace a document
- **Idempotent** **[HOVER: idempotent]** — doing it twice has same effect as doing it once

**[HOVER: idempotent]**_Idempotent means repeating the operation produces the same result as doing it once. PUT is idempotent: updating a user's email to "[new@example.com](mailto:new@example.com)" 10 times leaves the email as "[new@example.com](mailto:new@example.com)" (same result as doing it once). POST is NOT idempotent: submitting a payment form 10 times creates 10 payments. This matters for retry logic and reliability._

**PATCH** → "Change just this part" (partial update)

- Update only user's email
- Change one field
- More efficient than PUT

**DELETE** → "Remove this"

- Delete a user
- Remove a post

**[IMAGE: Visual showing each method with an icon and example: GET (magnifying glass), POST (plus sign), PUT (replace arrows), PATCH (pencil), DELETE (trash can)]**

**In code:**

```jsx
// Node.js with axios
axios.get('<https://api.example.com/users>')      // Retrieve users
axios.post('<https://api.example.com/users>', data) // Create user
axios.put('<https://api.example.com/users/1>', data) // Replace user 1
axios.patch('<https://api.example.com/users/1>', data) // Update user 1
axios.delete('<https://api.example.com/users/1>')  // Delete user 1
```

```python
# Django view handling methods
def user_detail(request, user_id):
    if request.method == 'GET':
        # Return user data
        user = User.objects.get(id=user_id)
        return JsonResponse({'user': user.to_dict()})

    elif request.method == 'PATCH':
        # Update user
        data = json.loads(request.body)
        user = User.objects.get(id=user_id)
        user.email = data.get('email', user.email)
        user.save()
        return JsonResponse({'status': 'updated'})

    elif request.method == 'DELETE':
        # Delete user
        User.objects.get(id=user_id).delete()
        return JsonResponse({'status': 'deleted'})
```

**Why this matters:** Using the wrong method confuses APIs and breaks caching. Don't use GET to change data (search engines and browsers assume GET is safe to repeat). Don't use POST when PATCH makes more sense.

---

### **2. The URL: Where Are You Asking?**

The **URL** **[HOVER: URL (detailed)]** specifies exactly what resource you want.

**[HOVER: URL (detailed)]**_URL (Uniform Resource Locator) is the address of a resource on the web. It has several parts: protocol (https://), domain ([api.example.com](http://api.example.com)), path (/users/123), query string (?sort=name&page=2), and sometimes fragment (#section). Each part serves a specific purpose in identifying what you're requesting and how._

Let's break down a URL:

```
<https://api.example.com/users/123?include=posts&sort=date#top>
│     ││              ││       ││                        │└─ Fragment (client-side only)
│     ││              ││       │└─ Query parameters
│     ││              ││       └─ Path parameters
│     ││              │└─ Path
│     │└─ Domain
│     └─ Protocol
```

**[ANIMATION: Interactive breakdown showing each URL component highlighted as you hover over it]**

**Protocol:** `https://`

- `http` = Unencrypted (don't use for anything sensitive)
- `https` = Encrypted with TLS (always use this)

**Domain:** `api.example.com`

- Which server to talk to
- Gets resolved to an IP via DNS (remember Part 1?)

**Path:** `/users/123`

- What resource you want
- `/users` = collection
- `/users/123` = specific user (ID 123)

**Query parameters:** `?include=posts&sort=date`

- Extra options, filters, settings
- Start with `?`, separated by `&`
- Key-value pairs: `key=value`

**Fragment:** `#top`

- Client-side only (not sent to server)
- Used for scrolling to page sections

---

### **Path Parameters vs Query Parameters**

This confuses everyone at first. Let's clarify:

**Path parameters** (part of the URL path):

```
/users/123          ← User ID is in the path
/posts/456/comments ← Post ID in the path
```

**Use for:** Identifying **which specific resource** you're working with.

**Query parameters** (after the `?`):

```
/users?page=2&limit=10&sort=name
```

**Use for:** Optional **filtering, sorting, pagination** — things that modify what you get back but don't identify the core resource.

**[IMAGE: Side-by-side comparison showing "/users/123" labeled "This specific user" vs "/users?role=admin" labeled "Users filtered by role"]**

**Example — both together:**

```
/users/123/posts?published=true&limit=5
│         ││                           │
│         ││                           └─ Query: filter & limit
│         │└─ Path: which user's posts
│         └─ Path: posts collection
└─ Path: users collection
```

**In code:**

```jsx
// Express - reading path and query params
app.get('/users/:id/posts', (req, res) => {
  const userId = req.params.id;         // Path param
  const published = req.query.published; // Query param
  const limit = req.query.limit;        // Query param

  // Get user's posts with filters
  const posts = getPostsForUser(userId, { published, limit });
  res.json(posts);
});
```

```python
# Django - URL patterns and views
from django.urls import path

urlpatterns = [
    path('users/<int:id>/posts', get_user_posts),
]

def get_user_posts(request, id):
    # Path param from URL
    user_id = id

    # Query params from request.GET
    published = request.GET.get('published')
    limit = request.GET.get('limit', 10)

    posts = Post.objects.filter(
        user_id=user_id,
        published=published
    )[:limit]

    return JsonResponse({'posts': [p.to_dict() for p in posts]})
```

---

### **3. Headers: The Metadata**

**Headers** **[HOVER: HTTP headers]** are key-value pairs that provide **information about the request**.

**[HOVER: HTTP headers]**_HTTP headers are metadata sent with requests and responses. They're like the envelope and stamps on a letter — they don't contain the main message, but they control how the message is handled. Headers specify content types, authentication, caching rules, and more. Both requests and responses have headers._

Think of headers as the **instructions on a package**:

- "Fragile"
- "Return address: ..."
- "Deliver by: ..."

Headers tell the server **how to interpret your request** and **what you expect back**.

**[IMAGE: Package with labels showing various header types as delivery instructions]**

### **Common Request Headers**

```jsx
// Example request with headers
axios.get('<https://api.example.com/users>', {
  headers: {
    'Authorization': 'Bearer eyJhbGc...',     // Auth token
    'Accept': 'application/json',            // I want JSON back
    'Accept-Language': 'en-US',              // Preferred language
    'User-Agent': 'MyApp/1.0',               // What client I am
    'X-Request-ID': 'abc-123'                // Custom tracking ID
  }
});
```

Let's break down what each header does:

**`Content-Type`** — Format of data you're **sending**

```jsx
'Content-Type': 'application/json'  // Sending JSON
'Content-Type': 'application/x-www-form-urlencoded'  // Form data
'Content-Type': 'multipart/form-data'  // File upload
```

**`Accept`** — Format of data you want **back**

```jsx
'Accept': 'application/json'  // Give me JSON
'Accept': 'text/html'         // Give me HTML
'Accept': 'image/png'         // Give me an image
```

**`Authorization`** — Credentials

```jsx
'Authorization': 'Bearer <token>'     // JWT token
'Authorization': 'Basic <base64>'     // Username:password (encoded)
'Authorization': 'API-Key <key>'      // API key (custom format)
```

**[HOVER: Bearer token]**_Bearer tokens are a type of access token, most commonly JWTs. "Bearer" means "whoever has this token has access" — like a ticket or key. The format is always "Bearer" followed by the token. This is the standard way to send authentication tokens in API requests. The server validates the token and grants access if it's valid._

**`Cookie`** — Browser automatically includes cookies

```jsx
'Cookie': 'sessionid=abc123; csrftoken=xyz789'
```

**[HOVER: Cookie header]**_The Cookie header is automatically set by browsers — you usually don't set it manually. It contains all cookies for that domain. Servers use cookies for session management (keeping you logged in). When the server sends Set-Cookie in a response, the browser stores it and automatically includes it in future requests to that domain._

**Custom headers** — Start with `X-`

```jsx
'X-API-Version': '2.0'
'X-Request-ID': 'unique-id'
'X-Custom-Metadata': 'whatever you want'
```

### **Reading Headers in Your Code**

**Express:**

```jsx
app.get('/api/data', (req, res) => {
  // Read incoming headers
  const token = req.headers['authorization'];
  const userAgent = req.headers['user-agent'];
  const customHeader = req.headers['x-custom-header'];

  console.log('Token:', token);
  console.log('User Agent:', userAgent);

  res.json({ received: true });
});
```

**Django:**

```python
def api_view(request):
    # Read incoming headers
    token = request.headers.get('Authorization')
    user_agent = request.headers.get('User-Agent')
    custom = request.headers.get('X-Custom-Header')

    # Headers are case-insensitive
    # HTTP_X_CUSTOM_HEADER works too (Django style)

    return JsonResponse({'received': True})
```

---

### **4. The Body: The Actual Data**

The **request body** **[HOVER: request body]** is where you put the main data you're sending.

**[HOVER: request body]**_The request body contains the main payload of your request — the data being sent. Only certain methods use bodies: POST, PUT, PATCH (not GET or DELETE typically). The body can be in different formats (JSON, form data, XML, binary) specified by the Content-Type header. Think of it as the contents of the package, while headers are the shipping label._

**Important:** Only **POST, PUT, PATCH** typically have bodies. GET and DELETE usually don't (they use URL parameters instead).

**Think of it as:** The actual contents of the package, while headers are the label.

### **Different Body Formats**

**JSON (most common for APIs):**

```jsx
// Sending JSON
axios.post('<https://api.example.com/users>',
  {
    name: 'Alice',
    email: 'alice@example.com',
    age: 28
  },
  {
    headers: { 'Content-Type': 'application/json' }
  }
);
```

```python
# Django receiving JSON
import json
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def create_user(request):
    if request.method == 'POST':
        # Parse JSON from request body
        data = json.loads(request.body)

        name = data.get('name')
        email = data.get('email')

        # Create user...
        return JsonResponse({'status': 'created', 'name': name})
```

**[HOVER: @csrf_exempt]**_@csrf_exempt disables Django's CSRF protection for this view. You need this for APIs that don't use session cookies (like JWT-based APIs). For session-based auth, keep CSRF protection enabled and send the CSRF token. Only use @csrf_exempt when you're sure it's safe (API with other auth methods)._

**Form data (traditional HTML forms):**

```jsx
// Sending form data
const formData = new URLSearchParams();
formData.append('name', 'Alice');
formData.append('email', 'alice@example.com');

axios.post('<https://api.example.com/users>', formData, {
  headers: { 'Content-Type': 'application/x-www-form-urlencoded' }
});
```

```python
# Django receiving form data
def create_user(request):
    if request.method == 'POST':
        # Form data is in request.POST
        name = request.POST.get('name')
        email = request.POST.get('email')

        return JsonResponse({'status': 'created'})
```

**File uploads (multipart):**

```jsx
// Sending file
const FormData = require('form-data');
const fs = require('fs');

const form = new FormData();
form.append('file', fs.createReadStream('./image.png'));
form.append('description', 'Profile picture');

axios.post('<https://api.example.com/upload>', form, {
  headers: form.getHeaders()  // Includes correct Content-Type with boundary
});
```

**[HOVER: multipart boundary]**_Multipart form data uses "boundaries" — unique strings that separate different parts of the data (file, text fields). The Content-Type header includes this boundary: "multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTr". You usually don't set this manually — libraries like FormData generate it automatically._

```python
# Django receiving file
def upload_file(request):
    if request.method == 'POST':
        # Files are in request.FILES
        uploaded_file = request.FILES['file']
        description = request.POST.get('description')

        # Save file...
        return JsonResponse({
            'filename': uploaded_file.name,
            'size': uploaded_file.size
        })
```

---

## **The Complete Request (What It Actually Looks Like)**

Here's what a real HTTP request looks like **in raw form** (you never write this manually, but understanding it helps):

```
POST /api/users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
User-Agent: axios/0.21.1
Content-Length: 58

{"name":"Alice","email":"alice@example.com","age":28}
```

**[ANIMATION: Interactive breakdown showing each line labeled with what it represents]**

Breaking it down:

- **Line 1:** Method, path, HTTP version
- **Lines 2-6:** Headers (key: value)
- **Blank line:** Separates headers from body
- **Last line:** The body (JSON data)

**Your code generates this.** When you do:

```jsx
axios.post('/api/users', { name: 'Alice', ... }, { headers: {...} })
```

Axios builds that HTTP message and sends it over the network.

---

## **Part 2: The Response (What You Get Back)**

When the server processes your request, it sends back a **response** with three main parts:

**[IMAGE: Response diagram showing Status Line, Headers, and Body]**

### **1. Status Line: Did It Work?**

The first line tells you if your request succeeded:

```
HTTP/1.1 200 OK
```

**Format:** `HTTP-version Status-Code Status-Text`

The **status code** **[HOVER: HTTP status codes]** is a three-digit number that tells you what happened.

**[HOVER: HTTP status codes]**_HTTP status codes are standardized three-digit numbers indicating the result of a request. The first digit indicates the category: 1xx (informational), 2xx (success), 3xx (redirection), 4xx (client error), 5xx (server error). They're essential for error handling — your code should check status codes to know if a request succeeded and how to respond to failures._

### **Status Code Categories**

**[IMAGE: Color-coded table showing status code ranges with icons]**

**2xx — Success** ✅

```
200 OK                 Everything worked, here's your data
201 Created            Resource created successfully
204 No Content         Success, but no data to return (common for DELETE)
```

**3xx — Redirection** ↪️

```
301 Moved Permanently  Resource moved, update your bookmarks
302 Found              Temporary redirect
304 Not Modified       Your cached version is still good (saves bandwidth)
```

**4xx — Client Error** ⚠️ (Your fault)

```
400 Bad Request        Malformed request (bad JSON, missing fields)
401 Unauthorized       Need to log in / provide auth
403 Forbidden          Logged in but not allowed to do this
404 Not Found          Resource doesn't exist
422 Unprocessable      Request valid but can't be processed (validation errors)
429 Too Many Requests  Rate limited, slow down
```

**5xx — Server Error** 💥 (Server's fault)

```
500 Internal Server Error  Something broke in the server code
502 Bad Gateway            Proxy couldn't reach the server
503 Service Unavailable    Server overloaded or down
504 Gateway Timeout        Server took too long to respond
```

**In code:**

```jsx
// Handling responses
axios.get('<https://api.example.com/users/123>')
  .then(response => {
    console.log('Status:', response.status);  // 200
    console.log('Data:', response.data);
  })
  .catch(error => {
    if (error.response) {
      // Server responded with error status
      console.log('Status:', error.response.status);  // 404, 500, etc.
      console.log('Error data:', error.response.data);

      if (error.response.status === 404) {
        console.log('User not found');
      } else if (error.response.status === 401) {
        console.log('Need to log in');
      }
    }
  });
```

```python
# Django sending different status codes
from django.http import JsonResponse, HttpResponse

def get_user(request, user_id):
    try:
        user = User.objects.get(id=user_id)
        return JsonResponse({'user': user.to_dict()})  # 200 by default

    except User.DoesNotExist:
        return JsonResponse(
            {'error': 'User not found'},
            status=404
        )

def create_user(request):
    # ... create user ...
    return JsonResponse(
        {'user': new_user.to_dict()},
        status=201  # Created
    )

def delete_user(request, user_id):
    User.objects.get(id=user_id).delete()
    return HttpResponse(status=204)  # No Content
```

---

### **2. Response Headers: Metadata About the Answer**

Just like request headers, response headers provide information about what's being sent back.

**Common Response Headers:**

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 1234
Cache-Control: no-cache, no-store
Set-Cookie: sessionid=abc123; HttpOnly; Secure
Access-Control-Allow-Origin: <https://yourfrontend.com>
X-RateLimit-Remaining: 99
```

**`Content-Type`** — What format is the body?

```
application/json   → JSON data
text/html          → HTML page
image/png          → PNG image
application/pdf    → PDF file
```

**`Content-Length`** — Size in bytes

**`Cache-Control`** — Caching rules (remember Part 4?)

```
max-age=3600       → Cache for 1 hour
no-cache           → Check if still valid before using cache
no-store           → Never cache (sensitive data)
```

**`Set-Cookie`** — Tell browser to store a cookie

```
Set-Cookie: sessionid=abc123; HttpOnly; Secure; SameSite=Lax
```

**`Access-Control-Allow-Origin`** — CORS settings (remember the security guide?)

```
*                           → Allow all origins (unsafe)
<https://yourfrontend.com>    → Allow specific origin
```

**Custom headers:**

```
X-RateLimit-Remaining: 99    → API calls left
X-Request-ID: unique-id      → For debugging
```

**Reading response headers:**

```jsx
// Express - sending headers
app.get('/api/data', (req, res) => {
  res.set('X-Custom-Header', 'MyValue');
  res.set('Cache-Control', 'max-age=3600');
  res.json({ data: 'here' });
});

// Axios - reading response headers
axios.get('<https://api.example.com/data>')
  .then(response => {
    console.log('Custom header:', response.headers['x-custom-header']);
    console.log('Content type:', response.headers['content-type']);
  });
```

```python
# Django - sending headers
def api_view(request):
    response = JsonResponse({'data': 'here'})
    response['X-Custom-Header'] = 'MyValue'
    response['Cache-Control'] = 'max-age=3600'
    return response
```

---

### **3. Response Body: The Actual Data**

The body contains what you asked for: JSON, HTML, an image, a file, whatever.

**JSON response (most common for APIs):**

```
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "name": "Alice",
  "email": "alice@example.com",
  "created_at": "2025-01-01T12:00:00Z"
}
```

**HTML response (for webpages):**

```
HTTP/1.1 200 OK
Content-Type: text/html

<!DOCTYPE html>
<html>
  <head><title>Hello</title></head>
  <body><h1>Welcome</h1></body>
</html>
```

**Error response (still JSON, but with error details):**

```
HTTP/1.1 400 Bad Request
Content-Type: application/json

{
  "error": "Validation failed",
  "details": {
    "email": ["Email is required", "Email must be valid"]
  }
}
```

---

## **Where Should Data Go? The Decision Tree**

This is the question that confuses everyone: **"I need to send this data... where do I put it?"**

Here's a clear decision tree:

**[IMAGE: Flowchart showing decision logic for where to put data]**

### **1. Identifying a Resource → Path Parameters**

If you're saying **"which specific thing"**, use the URL path:

```
GET /users/123           ← Which user
GET /posts/456/comments  ← Which post's comments
DELETE /products/789     ← Which product to delete
```

**Rule:** If removing it makes the request meaningless, it belongs in the path.

---

### **2. Authentication → Headers**

**Always put auth tokens in headers:**

```jsx
axios.get('/api/users', {
  headers: {
    'Authorization': 'Bearer your-token-here'
  }
});
```

**❌ Never do this:**

```jsx
// DON'T put tokens in the URL
axios.get('/api/users?token=your-token-here')  // INSECURE!
```

**Why?** URLs appear in:

- Browser history
- Server logs
- Referrer headers
- Browser extensions
- Anywhere that logs URLs

**Tokens in URLs leak.** Headers don't get logged the same way.

---

### **3. Filtering, Sorting, Pagination → Query Parameters**

For **optional modifiers** that don't identify the resource:

```
GET /users?role=admin&sort=name&page=2&limit=10
```

These are **options** — without them, the request still makes sense (you'd just get different results).

**[IMAGE: Comparison showing "/users/123" (required) vs "/users?role=admin" (optional filter)]**

---

### **4. Creating/Updating Data → Request Body**

For the **main payload** of data you're creating or updating:

```jsx
POST /api/users
Body: { "name": "Alice", "email": "alice@example.com" }

PATCH /api/users/123
Body: { "email": "newemail@example.com" }
```

**Why body instead of query params?**

- **Size:** Bodies have no practical size limit. URLs are limited (~2000 chars).
- **Security:** Bodies aren't logged like URLs.
- **Semantics:** Query params are for **getting** data, bodies are for **sending** data.

---

### **5. Metadata → Headers**

For **information about the request** (not the data itself):

```jsx
headers: {
  'Content-Type': 'application/json',  // Format I'm sending
  'Accept': 'application/json',        // Format I want back
  'Accept-Language': 'en-US',          // My language preference
  'User-Agent': 'MyApp/1.0',           // What client I am
  'X-Request-ID': 'abc-123'            // Custom tracking
}
```

---

## **Common Patterns & Best Practices**

### **Pattern 1: Getting a List with Filters**

```jsx
// Good
GET /api/users?role=admin&active=true&page=1&limit=20

// Reading in Express
app.get('/api/users', (req, res) => {
  const role = req.query.role;
  const active = req.query.active === 'true';
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 10;

  // Query database with filters...
});
```

```python
# Django
def list_users(request):
    role = request.GET.get('role')
    active = request.GET.get('active') == 'true'
    page = int(request.GET.get('page', 1))
    limit = int(request.GET.get('limit', 10))

    users = User.objects.filter(role=role, active=active)
    # Paginate...
```

---

### **Pattern 2: Creating a Resource**

```jsx
// Good
POST /api/users
Headers: {
  'Authorization': 'Bearer token',
  'Content-Type': 'application/json'
}
Body: {
  "name": "Alice",
  "email": "alice@example.com",
  "role": "admin"
}

// Express
app.post('/api/users', authenticateToken, (req, res) => {
  const { name, email, role } = req.body;

  // Validate
  if (!name || !email) {
    return res.status(400).json({ error: 'Name and email required' });
  }

  // Create user...
  res.status(201).json({ user: newUser });
});
```

```python
# Django
from django.views.decorators.csrf import csrf_exempt
import json

@csrf_exempt  # If using token auth
def create_user(request):
    if request.method != 'POST':
        return JsonResponse({'error': 'Method not allowed'}, status=405)

    data = json.loads(request.body)
    name = data.get('name')
    email = data.get('email')

    if not name or not email:
        return JsonResponse(
            {'error': 'Name and email required'},
            status=400
        )

    # Create user...
    return JsonResponse({'user': user_dict}, status=201)
```

---

### **Pattern 3: Updating a Resource**

```jsx
// Full replacement (PUT)
PUT /api/users/123
Body: { "name": "Alice Updated", "email": "new@example.com", "role": "user" }

// Partial update (PATCH) - preferred
PATCH /api/users/123
Body: { "email": "new@example.com" }  // Only what's changing

// Express
app.patch('/api/users/:id', (req, res) => {
  const userId = req.params.id;
  const updates = req.body;

  // Update only provided fields...
  res.json({ user: updatedUser });
});
```

---

### **Pattern 4: File Upload with Metadata**

```jsx
// Multipart form data
POST /api/upload
Headers: { 'Content-Type': 'multipart/form-data' }
Body:
  - file: [binary data]
  - description: "Profile picture"
  - category: "avatar"

// Node.js with multer
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

app.post('/api/upload', upload.single('file'), (req, res) => {
  const file = req.file;              // File
  const description = req.body.description;  // Text fields
  const category = req.body.category;

  res.json({
    filename: file.filename,
    description,
    category
  });
});
```

```python
# Django
def upload_file(request):
    if request.method == 'POST':
        file = request.FILES['file']
        description = request.POST.get('description')
        category = request.POST.get('category')

        # Save file...
        return JsonResponse({
            'filename': file.name,
            'description': description
        })
```

---

## **Security Best Practices**

**❌ DON'T:**

```jsx
// Don't put sensitive data in URLs
GET /api/data?password=secret123
GET /api/users?api_key=abc123

// Don't trust Content-Type header alone
// (validate the actual data format)

// Don't send passwords unencrypted
POST /api/login
Body: { "password": "plain-text" }  // Over HTTP - BAD!
```

**✅ DO:**

```jsx
// Use headers for auth
GET /api/data
Headers: { 'Authorization': 'Bearer token' }

// Use HTTPS for anything sensitive
POST <https://api.example.com/login>
Body: { "password": "..." }  // Over HTTPS - encrypted

// Validate incoming data
app.post('/api/users', (req, res) => {
  const { email } = req.body;

  // Validate before using
  if (!isValidEmail(email)) {
    return res.status(400).json({ error: 'Invalid email' });
  }

  // Now safe to use...
});
```

---

## **Debugging HTTP Requests**

### **Browser DevTools**

Press F12 → Network tab → Make a request

You'll see:

- Method and URL
- Status code
- Request headers (sent)
- Response headers (received)
- Request body (payload)
- Response body (preview/response)
- Timing (how long each stage took)

**[IMAGE: Screenshot of browser DevTools Network tab with annotations pointing to key sections]**

This is **the best way to understand what's actually being sent.**

---

### **Command Line with cURL**

```bash
# Simple GET
curl <https://api.example.com/users>

# GET with headers
curl -H "Authorization: Bearer token" \
     -H "Accept: application/json" \
     <https://api.example.com/users>

# POST with JSON
curl -X POST <https://api.example.com/users> \
     -H "Content-Type: application/json" \
     -d '{"name":"Alice","email":"alice@example.com"}'

# See full request/response (verbose)
curl -v <https://api.example.com/users>
```

**[HOVER: cURL]**_cURL is a command-line tool for making HTTP requests. It's installed on most systems by default. It's extremely useful for testing APIs without writing code. The -v flag shows full request/response details, -H sets headers, -d sends data, -X specifies the method. Learn basic cURL — it's invaluable for debugging._

---

## **Key Takeaways**

✅ **HTTP is a structured conversation** — request (question) → response (answer)

✅ **Requests have four parts** — Method, URL, Headers, Body

✅ **Responses have three parts** — Status line, Headers, Body

✅ **Methods matter** — GET (read), POST (create), PUT (replace), PATCH (update), DELETE (remove)

✅ **URLs have structure** — Path params (identify resource), query params (filter/options)

✅ **Headers are for metadata** — Auth, content types, custom info

✅ **Body is for data** — The main payload being sent

✅ **Status codes tell you what happened** — 2xx success, 4xx your fault, 5xx server fault

✅ **Security matters** — Auth in headers (not URLs), use HTTPS, validate everything

✅ **Use the right place for data:**

- Path params → which resource
- Query params → filtering/options
- Headers → metadata/auth
- Body → main data payload

---

## **Final Thoughts**

HTTP isn't complicated. It's just **structured**.

Once you understand the structure — what goes where and why — it clicks. You stop copying Stack Overflow examples blindly. You can **read API docs and know exactly what they're asking for.**

When something goes wrong, you know where to look:

- 404? Check your URL/path
- 401? Check your Authorization header
- 400? Check your request body format
- 500? Server-side issue, check server logs

**You're now equipped to:**

- Make any kind of HTTP request
- Structure data correctly
- Debug when things go wrong
- Read API documentation confidently
- Build your own APIs properly

The next time you see "Send the API key in the X-API-Key header with a POST request containing JSON in the body," you'll know **exactly** what that means and how to do it.

Go build something. Make some requests. Break things. Fix them. That's how you get comfortable.

You've got this. 🚀

---

**Quick Reference Card:**

```
REQUEST STRUCTURE:
┌─────────────────────────────────────┐
│ POST /api/users/123/posts HTTP/1.1 │ ← Method, Path, Version
├─────────────────────────────────────┤
│ Host: api.example.com               │
│ Authorization: Bearer token         │ ← Headers
│ Content-Type: application/json      │
├─────────────────────────────────────┤
│ {"title":"Hello","content":"..."}   │ ← Body
└─────────────────────────────────────┘

RESPONSE STRUCTURE:
┌─────────────────────────────────────┐
│ HTTP/1.1 201 Created                │ ← Status Line
├─────────────────────────────────────┤
│ Content-Type: application/json      │
│ Cache-Control: no-cache             │ ← Headers
│ Set-Cookie: session=abc             │
├─────────────────────────────────────┤
│ {"id":456,"title":"Hello",...}      │ ← Body
└─────────────────────────────────────┘

WHERE TO PUT DATA:
Resource ID        → Path param      /users/123
Optional filters   → Query param     ?role=admin&page=2
Authentication     → Header          Authorization: Bearer token
Main data          → Body            {"name":"Alice",...}
Metadata          → Header          Content-Type, Accept, etc.
```
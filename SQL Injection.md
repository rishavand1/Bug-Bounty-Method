Below are **safe, educational examples** that illustrate **where** SQL injection testing is performed and **what kinds of inputs** you might examine during a legitimate security assessment or lab. The examples focus on identifying potential injection points rather than providing exploit payloads.

---

# Module 1 — URL Query Parameters

These are the most common SQL injection entry points.

### Example 1: Product Page

```http
GET /product?id=1 HTTP/1.1
Host: example.com
```

The application might execute:

```sql
SELECT * FROM products WHERE id = 1;
```

Possible observations:

```
?id=1      → Product 1
?id=2      → Product 2
?id=9999   → Product Not Found
```

This indicates the `id` parameter likely influences a database query.

---

### Example 2: User Profile

```http
GET /profile?user=15 HTTP/1.1
```

Backend concept:

```sql
SELECT * FROM users WHERE id = 15;
```

---

### Example 3: Blog

```http
GET /post?article=25 HTTP/1.1
```

Backend:

```sql
SELECT * FROM posts WHERE article_id = 25;
```

---

# Module 2 — Search Parameters

Example

```http
GET /search?q=laptop HTTP/1.1
```

Backend

```sql
SELECT *
FROM products
WHERE name LIKE '%laptop%';
```

Observe how changing the search term changes the results.

---

# Module 3 — Login Form (POST)

Request

```http
POST /login HTTP/1.1

username=admin
password=password123
```

Backend

```sql
SELECT *
FROM users
WHERE username='admin'
AND password='password123';
```

Any user-controlled input here deserves careful review during a security assessment.

---

# Module 4 — Registration Form

Request

```http
POST /register

username=vishu
email=test@example.com
city=Indore
```

Backend

```sql
INSERT INTO users
(username,email,city)
VALUES
(?,?,?)
```

A secure application uses parameterized queries rather than concatenating strings.

---

# Module 5 — Search Filters

URL

```http
GET /products?category=Shoes
```

Backend

```sql
SELECT *
FROM products
WHERE category='Shoes';
```

---

# Module 6 — Sorting

Example

```http
GET /products?sort=price
```

Backend

```sql
SELECT *
FROM products
ORDER BY price;
```

Sorting parameters are often overlooked and should be validated against an allowlist.

---

# Module 7 — Pagination

Example

```http
GET /products?page=3
```

Backend

```sql
SELECT *
FROM products
LIMIT 20 OFFSET 40;
```

---

# Module 8 — REST API

Example

```http
GET /api/users/25 HTTP/1.1
```

Backend

```sql
SELECT *
FROM users
WHERE id=25;
```

The identifier in the URL path may come directly from the request.

---

# Module 9 — JSON API

Request

```http
POST /api/login

{
    "username":"admin",
    "password":"password123"
}
```

Backend

```sql
SELECT *
FROM users
WHERE username=?
AND password=?;
```

Modern APIs frequently use JSON instead of form data.

---

# Module 10 — Cookie Values

Request

```http
Cookie:
user_id=25
theme=dark
```

Backend

```sql
SELECT *
FROM users
WHERE id=25;
```

Cookie values should be treated as untrusted input.

---

# Module 11 — HTTP Headers

Request

```http
GET / HTTP/1.1

User-Agent: Mozilla/5.0
```

Sometimes applications log headers:

```sql
INSERT INTO logs(user_agent)
VALUES (?);
```

If logged or queried later without proper handling, headers can become relevant in security reviews.

---

# Module 12 — Hidden Parameters

Normal request

```http
GET /products
```

Hidden parameter discovered

```http
GET /products?debug=1
```

or

```http
GET /products?preview=true
```

Such parameters may expose additional functionality.

---

# Module 13 — Second-Order SQL Injection (Concept)

### Step 1

User registers

```text
Username: Vishu
```

Stored in database.

---

### Step 2

Later, an administrator searches for users and the application reuses stored data in a new query.

The important lesson is that unsafe handling can occur **after** the data is stored, not necessarily when it is first submitted.

---

# Module 14 — Blind SQL Injection (Concept)

Suppose the application always returns:

```
Login Failed
```

No visible database error is shown.

Instead, you may observe differences such as:

* Response length changes
* Different HTTP status codes
* Different page content
* Response time changes

Those behavioral differences are clues that input is affecting the backend.

---

# Module 15 — Error-Based SQL Injection (Concept)

Application

```http
GET /product?id=5
```

If invalid input causes an error page like:

```
Database Error

Syntax error near ...
```

or

```
SQLSTATE...
```

that can indicate database errors are being exposed to the client, which is useful information for defenders and testers.

---

# Module 16 — Numeric Parameters

Examples

```http
/product?id=1

/product?id=2

/product?id=3
```

Backend

```sql
SELECT *
FROM products
WHERE id=3;
```

---

# Module 17 — String Parameters

Example

```http
GET /user?name=Alice
```

Backend

```sql
SELECT *
FROM users
WHERE name='Alice';
```

---

# Module 18 — Multiple Parameters

Example

```http
GET /products?category=Books&page=2&sort=price
```

Backend

```sql
SELECT *
FROM products
WHERE category=?
ORDER BY price
LIMIT ...
```

Each parameter should be assessed independently because they may influence different parts of the query.

---

# Module 19 — File Download

Example

```http
GET /download?file=invoice.pdf
```

Backend

```sql
SELECT path
FROM files
WHERE filename='invoice.pdf';
```

---

# Module 20 — Report Generation

Example

```http
GET /report?year=2026
```

Backend

```sql
SELECT *
FROM sales
WHERE year=2026;
```

---

## What to Practice

As you inspect requests in tools like **Burp Suite**, ask yourself these questions for every parameter:

| Question                                              | Example                                         |
| ----------------------------------------------------- | ----------------------------------------------- |
| Is this value coming from the user?                   | `id=10`                                         |
| Does changing it change the page?                     | Product changes                                 |
| Does it return a database error?                      | Error page appears                              |
| Is it numeric or text?                                | `id=10` vs `name=Alice`                         |
| Is it in the URL, POST body, JSON, cookie, or header? | All are possible input sources                  |
| Could it influence a SQL query?                       | Product lookup, login, search, sorting, reports |

This mindset is what experienced bug bounty hunters use to systematically identify potential SQL injection points before attempting any deeper testing in authorized environments.

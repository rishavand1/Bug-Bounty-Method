# Cross-Site Scripting (XSS) for Bug Bounty Hunters

**Cross-Site Scripting (XSS)** occurs when a web application includes untrusted user input in a webpage without proper encoding or sanitization, allowing JavaScript to execute in another user's browser.

> **Note:** The examples below use the harmless JavaScript function `alert()` because it's the standard way to demonstrate XSS in training labs. Only test applications you own or are explicitly authorized to assess.

---

# Types of XSS

1. Reflected XSS
2. Stored XSS
3. DOM-Based XSS
4. Blind XSS
5. Self XSS (not usually considered a security vulnerability)

---

# 1. Reflected XSS

## Scenario

A search page:

```http
GET /search?q=phone HTTP/1.1
Host: example.com
```

The server responds with:

```html
<h2>You searched for phone</h2>
```

### Vulnerable server-side code (concept)

```php
echo "<h2>You searched for " . $_GET['q'] . "</h2>";
```

User input is written directly into HTML.

### Safe demonstration

If a training lab reflects a harmless script like:

```html
<script>alert("XSS")</script>
```

and it executes in the browser, the page is vulnerable.

---

# 2. Stored XSS

## Scenario

Comment section

User submits:

```
Name: Vishu

Comment:
Hello everyone!
```

Database:

```text
Name      Comment
----------------------------
Vishu     Hello everyone!
```

Later the webpage displays:

```html
<div>
Hello everyone!
</div>
```

If the application stores unsanitized HTML/JavaScript, every visitor viewing the comment could trigger the script.

Typical places:

* Blog comments
* Reviews
* Forums
* User profiles
* Chat applications
* Ticket systems

---

# 3. DOM-Based XSS

The vulnerability exists in client-side JavaScript.

Example page:

```html
<script>
document.getElementById("result").innerHTML =
location.hash.substring(1);
</script>
```

URL:

```
https://example.com/#Hello
```

Output:

```
Hello
```

If untrusted data from the URL is inserted into the DOM using unsafe APIs like `innerHTML`, it may become an XSS vulnerability.

---

# 4. Blind XSS

A user submits data into a form:

```
Feedback
Contact Form
Support Ticket
```

Nothing happens immediately.

Later, an administrator opens the submitted content in an internal dashboard.

If unsafe input is rendered there, the administrator's browser executes it.

Common locations:

* Admin panels
* Help desk systems
* CRM software
* Log viewers
* Internal dashboards

---

# 5. Self XSS

Example:

A website tells users:

```
Open Developer Tools
Paste this code
Press Enter
```

The script only runs because the user pasted it into their own browser.

Since it requires social engineering rather than exploiting the application itself, self-XSS is generally **not** considered a valid security vulnerability.

---

# Common XSS Injection Points

## Search Box

```http
GET /search?q=laptop
```

---

## Login Page

```
Username

Password
```

Error messages or reflected input may be vulnerable.

---

## Contact Form

```
Name
Email
Message
```

---

## Blog Comments

```
Author
Comment
Website
```

---

## User Profile

```
Display Name
Bio
Location
Website
```

---

## Reviews

```
Rating
Title
Review
```

---

## Support Tickets

```
Subject

Description
```

---

## Chat Applications

```
Message
```

---

## URL Parameters

```
?id=10

?name=Alice

?q=Phone

?category=Laptop
```

---

## HTTP Headers

Applications sometimes display or log values from:

* `User-Agent`
* `Referer`
* `X-Forwarded-For`

If those values are later rendered into HTML without encoding, they can become XSS vectors.

---

# XSS Contexts

Understanding the **context** where your input appears is critical.

## HTML Context

```html
<div>YOUR INPUT</div>
```

---

## HTML Attribute Context

```html
<input value="YOUR INPUT">
```

---

## JavaScript Context

```html
<script>

var name="YOUR INPUT";

</script>
```

---

## URL Context

```html
<a href="YOUR INPUT">
```

---

## CSS Context

```html
<style>

background:url(YOUR INPUT);

</style>
```

Each context requires different output encoding to be safe.

---

# Places Worth Testing

* Search pages
* Login forms
* Registration forms
* Contact forms
* Profile pages
* Settings pages
* Comments
* Reviews
* Forums
* Ticket systems
* Chat applications
* Admin panels
* Error messages
* URL parameters
* Cookies (if reflected)
* HTTP headers (if reflected)
* Markdown editors
* Rich-text editors
* File names displayed in the UI

---

# Indicators of a Possible XSS Issue

Watch for situations where your input is reflected or stored without proper encoding:

* Your text appears exactly as entered in the page source.
* HTML tags you entered are rendered as HTML instead of plain text.
* Data from the URL is inserted into the page dynamically by JavaScript.
* User-generated content is displayed to other users.

---

# Safe Practice Labs

The best places to learn XSS legally are:

* PortSwigger Web Security Academy — Comprehensive XSS labs from beginner to advanced.
* OWASP Juice Shop
* OWASP WebGoat
* DVWA

---

# Learning Roadmap

1. HTML basics
2. How browsers render HTML
3. JavaScript fundamentals
4. Reflected XSS
5. Stored XSS
6. DOM-based XSS
7. Output encoding
8. HTML, attribute, JavaScript, URL, and CSS contexts
9. Browser security features (such as Content Security Policy)
10. Advanced XSS topics like filter bypasses and framework-specific issues (in authorized labs)

Mastering the **context** in which user input is rendered is often more important than memorizing individual payloads. Experienced testers first identify *where* and *how* input is reflected, then determine whether the application safely encodes it before concluding whether an XSS vulnerability exists.

If you're serious about web bug bounty hunting, URL manipulation is one of the highest-ROI skills you can learn. Many vulnerabilities start with understanding how an application processes URLs.

Here's a structured roadmap of URL testing topics that professional bug bounty hunters study.

---

# URL Testing Lessons for Bug Bounty Hunters

## Module 1 — URL Fundamentals

Learn:

* URL structure
* Absolute vs Relative URLs
* URL Encoding
* URL Decoding
* Reserved characters
* Query Parameters
* Fragments
* Paths
* Schemes

Example

```
https://example.com/admin/login?id=10&user=admin#profile
```

Break it into

```
Scheme
Host
Port
Path
Query
Fragment
```

Practice

```
?id=1
?id=2
?id=999

?page=home
?page=admin
```

---

# Module 2 — HTTP Methods

Understand how URLs behave differently with

```
GET
POST
PUT
DELETE
PATCH
OPTIONS
HEAD
TRACE
```

Questions

* Does GET expose sensitive data?
* Can POST become GET?
* Can DELETE be changed into GET?

---

# Module 3 — Query Parameter Testing

Test every parameter.

Example

```
?id=5

↓

?id=1
?id=0
?id=-1
?id=999999
?id='
?id="
?id=null
?id=[]
?id={}
```

Look for

* SQL Injection
* IDOR
* XSS
* Information Disclosure
* Broken Logic

---

# Module 4 — Hidden Parameters

Discover parameters using

* Burp Param Miner
* Arjun
* ffuf
* wordlists

Example

```
?page=home

Try

?admin=true

?debug=true

?test=1

?preview=true

?internal=1
```

---

# Module 5 — Path Manipulation

Example

```
/user/1

↓

/user/2
/user/3
/user/999
```

Test

```
../
..
%2e%2e/
```

Also

```
/api/
/v1/
/v2/
/backup/
/old/
```

---

# Module 6 — URL Encoding

Learn

```
Space

%20

+

%2b

/

%2f

.

%2e

..

%2e%2e

#

%23

?

%3f

&
%26
```

Then learn

Double Encoding

```
../

↓

%2e%2e/

↓

%252e%252e/
```

Useful for bypasses.

---

# Module 7 — Path Traversal

Practice

```
../../etc/passwd

..%2f

..%252f

....//

..%c0%af

..%255c
```

Learn Windows paths too.

```
..\

C:\Windows\win.ini
```

---

# Module 8 — Open Redirect Testing

Example

```
?next=

?redirect=

?url=

?return=

?continue=
```

Payloads

```
https://evil.com

//evil.com

////evil.com

https:%2f%2fevil.com

//google.com
```

---

# Module 9 — File Download Parameters

Example

```
download=file.pdf

↓

download=config.php

download=.env

download=backup.zip

download=database.sql
```

---

# Module 10 — File Inclusion

Example

```
?page=home

↓

?page=about

↓

?page=../../etc/passwd
```

Also test

```
php://filter

php://input

file://

expect://
```

---

# Module 11 — API Endpoint Discovery

Start with

```
/api

/api/v1

/api/v2

/graphql

/swagger

/openapi

/docs

/redoc
```

---

# Module 12 — URL Normalization

Servers normalize URLs differently.

Examples

```
/admin

//admin

///admin

/%2e/admin

/admin/.

/admin//

/./admin
```

Interesting findings include

* Authentication bypass
* WAF bypass
* Routing confusion

---

# Module 13 — Host Header & URL Confusion

Test

```
Host:

X-Forwarded-Host

X-Original-Host

Forwarded
```

Look for

* Password reset poisoning
* Cache poisoning
* SSRF

---

# Module 14 — URL Rewrite Rules

Examples

```
/login

/login/

/login/index.php

/login.php

/index.php/login
```

Different routes may expose different behaviors.

---

# Module 15 — Extension Manipulation

Example

```
report.pdf

↓

report.php

report.json

report.xml

report.txt

report.zip

report.tar.gz
```

---

# Module 16 — Case Sensitivity

Test

```
/Admin

/admin

/ADMIN

/aDmIn
```

Some servers treat these as distinct resources.

---

# Module 17 — Parameter Pollution (HPP)

Example

```
?id=1&id=2

?page=admin&page=user
```

Or

```
?role=user&role=admin
```

Different frameworks resolve duplicates differently.

---

# Module 18 — Matrix Parameters

Some frameworks support

```
/users;id=1

/admin;debug=true
```

Often overlooked.

---

# Module 19 — Fragment Testing

```
#admin

#redirect

#token
```

While fragments are not sent to the server, client-side JavaScript may process them, leading to DOM-based vulnerabilities.

---

# Module 20 — URL-Based Access Control

Test changing

```
/admin

↓

/user

↓

/manager

↓

/internal
```

Try

```
/api/admin

/api/internal

/api/private

/api/debug
```

---

# Module 21 — REST API Manipulation

```
GET /users/5

↓

GET /users/6

↓

GET /users/100
```

Classic place to find IDORs.

---

# Module 22 — URL Fuzzing

Tools

* Burp Intruder
* ffuf
* feroxbuster
* dirsearch
* gobuster

Common wordlists

* SecLists
* Assetnote
* OneListForAll

---

# Module 23 — CDN and Cache Key Testing

Test whether changing URLs affects cache behavior.

Examples

```
?a=1

?utm=test

?cache=1

?_=123

?cb=random
```

Useful for identifying cache poisoning or cache deception issues.

---

# Module 24 — URL Parsing Differences

Different layers (browser, proxy, WAF, backend framework) may interpret the same URL differently.

Examples

```
https://example.com\@evil.com

https://example.com%2Fadmin

https://example.com//admin

https://example.com/%2e/admin
```

These inconsistencies can sometimes lead to authentication bypasses or request smuggling scenarios.

---

# Recommended Labs

To practice these techniques legally and safely:

* PortSwigger Web Security Academy — Free labs covering path traversal, IDOR, SSRF, open redirects, XSS, and more.
* OWASP Juice Shop — Practice common web vulnerabilities in a realistic application.
* OWASP WebGoat — Guided lessons for web application security.
* DVWA — Classic vulnerable application for learning.
* PortSwigger Academy Labs — Excellent for mastering URL manipulation in real attack scenarios.

---

## Learning Order

1. URL Basics
2. HTTP Methods
3. Parameters
4. Encoding & Decoding
5. Path Traversal
6. Open Redirects
7. Hidden Parameters
8. REST APIs
9. IDOR
10. URL Fuzzing
11. URL Normalization
12. Host Header Attacks
13. Cache Poisoning
14. Advanced URL Parsing

Mastering these topics will prepare you to identify a wide range of vulnerabilities that stem from how web applications parse, route, and authorize requests based on URLs.

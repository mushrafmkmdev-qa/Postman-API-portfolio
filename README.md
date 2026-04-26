# FakeStoreAPI – QA Portfolio Project

**By Mushraf Kadar Mohideen | Manual QA Tester**
📧 mushraf.mkm.01@gmail.com | 📍 Tiruchirappalli, India | 🌐 Open to Remote

---

## About This Project

This is an end-to-end API test suite built in Postman for the [FakeStoreAPI](https://fakestoreapi.com) — a public REST API that simulates a real e-commerce backend with Auth, Products, and Cart modules.

The project demonstrates my ability to design and execute meaningful API tests, write assertion scripts, perform negative testing, and document defects — skills I've applied in 9 months of crowd-testing on Test.io across 40+ test cycles for global clients.

---

## API Under Test

| Detail | Value |
|---|---|
| API | FakeStoreAPI |
| Base URL | `https://fakestoreapi.com` |
| Type | REST API |
| Auth | JWT Token (Bearer) |
| Docs | https://fakestoreapi.com/docs |

---

## Test Coverage

### Auth Module
| Request | Method | Test Type |
|---|---|---|
| Login – valid credentials | POST | Functional, Token validation |
| Login – invalid credentials | POST | Negative testing |

### Products Module
| Request | Method | Test Type |
|---|---|---|
| Get all products | GET | Functional, Schema, Performance |
| Get single product | GET | Functional, Schema validation |
| Get invalid product ID | GET | Negative testing, Boundary |
| Create product | POST | Functional, Response validation |
| Delete product | DELETE | Functional |

### Cart Module
| Request | Method | Test Type |
|---|---|---|
| Get user cart | GET | Functional |
| Add to cart | POST | Functional, Response validation |
| Delete cart | DELETE | Functional |

**Total: 10 requests | 26 test assertions**

---

## Test Script Highlights

**Token chaining — login saves token automatically for next requests:**
```javascript
pm.test("Token is returned", function () {
    var json = pm.response.json();
    pm.expect(json.token).to.be.a("string");
    pm.environment.set("token", json.token);
});
```

**Schema validation — verifying field types and values:**
```javascript
pm.test("Schema is valid", function () {
    var json = pm.response.json();
    pm.expect(json).to.have.property("id");
    pm.expect(json).to.have.property("title");
    pm.expect(json).to.have.property("price");
    pm.expect(json.price).to.be.a("number").and.above(0);
});
```

**Negative test — invalid product ID returns null:**
```javascript
pm.test("Body is null for missing product", function () {
    pm.expect(pm.response.text()).to.satisfy(function(body) {
        return body === "null" || body.includes("404");
    });
});
```

**Response time SLA — all GET requests must respond under 1000ms:**
```javascript
pm.test("Response under 1000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(1000);
});
```

---

## Skills Demonstrated

- REST API testing with Postman (GET, POST, PUT, DELETE)
- Test case design — functional, negative, boundary, schema validation
- Environment variables and token chaining across requests
- Response time / performance assertions
- Bug identification and professional defect reporting
- Collection Runner for full suite execution
- Newman CLI for command-line automation

---

## Project Structure

```
postman-api-portfolio/
├── collections/
│   └── FakeStoreApi_postman_collection.json
├── environments/
│   └── Dev.postman_environment.json
├── reports/
│   └── newman-report.html
├── bug-reports/
│   └── BUG-REPORT.md
└── README.md
```

---

## How to Run Locally

### Option 1 – Postman UI
1. Import `collections/FakeStoreApi_postman_collection.json` into Postman
2. Import `environments/Dev.postman_environment.json` and select it
3. Open Collection Runner and run the full suite

### Option 2 – Newman CLI
```bash
# Install Newman
npm install -g newman newman-reporter-html

# Run the collection and generate HTML report
newman run collections/FakeStoreApi_postman_collection.json \
  -e environments/Dev.postman_environment.json \
  --reporters cli,html \
  --reporter-html-export reports/newman-report.html
```

---

## Sample Test Results

| Test | Status |
|---|---|
| Login valid – status 201 | ✅ Pass |
| Login valid – token returned | ✅ Pass |
| Login valid – response under 1000ms | ✅ Pass |
| Login invalid – status 400 or 401 | ✅ Pass |
| Login invalid – error message present | ✅ Pass |
| Login invalid – response under 1000ms | ✅ Pass |
| Get all products – status 200 | ✅ Pass |
| Get all products – is array | ✅ Pass |
| Get all products – not empty | ✅ Pass |
| Get all products – field check | ✅ Pass |
| Get all products – response under 1000ms | ✅ Pass |
| Get single product – status 200 | ✅ Pass |
| Get single product – schema valid | ✅ Pass |
| Get invalid product – status 200 or 404 | ✅ Pass |
| Get invalid product – body is null | ❌ Fail (API defect – see BR-002) |
| Create product – status 200 or 201 | ✅ Pass |
| Create product – ID returned | ✅ Pass |
| Delete product – status 200 | ✅ Pass |
| Delete product – ID returned | ✅ Pass |
| Get user cart – status 200 | ✅ Pass |
| Get user cart – is array | ✅ Pass |
| Add to cart – status 200 or 201 | ✅ Pass |
| Add to cart – cart ID returned | ✅ Pass |
| Add to cart – response under 1000ms | ✅ Pass |
| Delete cart – status 200 | ✅ Pass |
| Delete cart – ID returned | ✅ Pass |

**25 passed / 1 failed (known API defect — documented in BR-002)**

---

## Bugs Found During Testing

While executing tests, 3 defects were identified across Auth, Products, and Cart modules.
See [`bug-reports/BUG-REPORT.md`](bug-reports/BUG-REPORT.md) for full reports with reproduction
steps, severity, and expected vs actual results.

**Notable finding:** The login endpoint returns `201` instead of the industry-standard
`200 OK` for a successful authentication response. POST endpoints that perform an action
(like login) should return `200 OK`. `201 Created` is reserved for endpoints that create
a new resource (like `POST /products`). This has been documented as BR-004.

---

## About Me

I'm a Manual QA Tester with 9 months of hands-on experience in crowd-testing via Test.io —
completing 40+ test cycles, submitting 38 user stories, and getting 22 bugs approved across
web and mobile applications for global clients.

**Core skills:** Postman · SQL · JIRA · Chrome DevTools · Charles Proxy · Selenium (in progress)

**Currently learning:** Selenium WebDriver · TestNG · Page Object Model (POM)

📬 Open to remote QA roles — India and international (IST-compatible)

---

## Connect

- Email: mushraf.mkm.01@gmail.com
- Phone: +91 7708177351
- Location: Tiruchirappalli, India

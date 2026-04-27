# FakeStoreAPI – QA Portfolio Project

**By Mushraf Kadar Mohideen | Manual QA Tester**
📧 mushraf.mkm.01@gmail.com | 📍 Tiruchirappalli, India | 🌐 Open to Remote

---

## About This Project

This is an end-to-end API test suite built in Postman for the [FakeStoreAPI](https://fakestoreapi.com) — a public REST API that simulates a real e-commerce backend with Auth, Products, and Cart modules.

The project demonstrates my ability to design and execute meaningful API tests, write assertion scripts, perform negative testing, and document defects — skills I have applied in 9 months of crowd-testing on Test.io across 40+ test cycles for global clients.

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
| Login – valid credentials | POST | Functional, Token validation, Performance |
| Login – invalid credentials | POST | Negative testing, Performance |

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
| Add to cart | POST | Functional, Response validation, Performance |
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

**Negative test — invalid product ID returns empty or null body:**
```javascript
pm.test("Body is empty or null for missing product", function () {
    pm.expect(pm.response.text()).to.satisfy(function(body) {
        return body === "null" || body === "" || body.includes("404");
    });
});
```

**Response time SLA — requests must respond under 1000ms:**
```javascript
pm.test("Response under 1000ms", function () {
    pm.expect(pm.response.responseTime).to.be.below(1000);
});
```

**Negative test — invalid credentials are rejected:**
```javascript
pm.test("Status is 400 or 401", function () {
    pm.expect(pm.response.code).to.be.oneOf([400, 401]);
});
```

---

## Skills Demonstrated

- REST API testing with Postman (GET, POST, DELETE)
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
│   └── FakeStoreApi.postman_collection.json
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
1. Import `collections/FakeStoreApi.postman_collection.json` into Postman
2. Import `environments/Dev.postman_environment.json` and select it
3. Open Collection Runner and click Run

### Option 2 – Newman CLI
```bash
# Install Newman
npm install -g newman newman-reporter-html

# Run by passing base_url directly
newman run collections/FakeStoreApi.postman_collection.json \
  --env-var "base_url=https://fakestoreapi.com" \
  --reporters cli,html \
  --reporter-html-export reports/newman-report.html
```

---

## Newman Run Results

| Detail | Value |
|---|---|
| Environment | Dev |
| Iterations | 1 |
| Total requests | 10 |
| Total assertions | 26 |
| Passed | 26 |
| Failed | 0 |
| Errors | 0 |
| Avg. Response Time | 342ms |

### Full Test Results

| Request | Response Code | Tests | Status |
|---|---|---|---|
| Login-Valid | 201 | Status is 201, Token is returned, Response under 1000ms | ✅ 3/3 |
| Login-Invalid | 401 | Status is 400 or 401, Response under 1000ms, Error message is present | ✅ 3/3 |
| Get all products | 200 | Status is 200, Is array, Not empty, Response under 1000ms, Field check | ✅ 5/5 |
| Get Single Products | 200 | Status is 200, Schema is valid | ✅ 2/2 |
| Get invalid product | 200 | Status is 200 or 404, Body is empty or null | ✅ 2/2 |
| Create product | 201 | Status is 200 or 201, ID returned | ✅ 2/2 |
| Delete product | 200 | Status is 200, ID returned | ✅ 2/2 |
| Get user cart | 200 | Status is 200, Is array | ✅ 2/2 |
| Add to cart | 201 | Status is 200 or 201, Cart ID returned, Response under 1000ms | ✅ 3/3 |
| Delete cart | 200 | Status is 200, ID returned | ✅ 2/2 |

**26 passed / 0 failed**

---

## Bugs Found During Testing

3 defects were identified and documented during test execution.
See [`bug-reports/BUG-REPORT.md`](bug-reports/BUG-REPORT.md) for full reports.

| Bug ID | Module | Summary | Severity |
|---|---|---|---|
| BR-001 | Products | GET /products/999 returns 200 + empty body instead of 404 | Medium |
| BR-002 | Cart | POST /carts accepts empty request body | Low |
| BR-003 | Auth | POST /auth/login returns 201 instead of standard 200 | Low |

---

## About Me

I am a Manual QA Tester with 9 months of hands-on experience in crowd-testing via Test.io —
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

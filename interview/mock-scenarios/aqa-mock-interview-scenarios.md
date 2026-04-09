# AQA Mock Interview Scenarios

## What

Collection of mock interview scenarios with expected answer direction and feedback criteria.

## Why

Practice with realistic prompts improves clarity, structure, and confidence before real interviews.

## How

### Scenario 1: REST Orders API

- define endpoints and HTTP methods;
- pick correct status codes (`201`, `204`, `401`, `403`, `404`, `504`);
- explain idempotency for `GET/PUT/DELETE`.

### Scenario 2: API Automation Strategy

- prioritize positive/negative/contract/security checks;
- show fixture/client architecture in pytest;
- justify where mocks are acceptable vs real integration.

### Scenario 3: pytest Project Structure

- folders by domain and protocol clients;
- shared fixtures in `conftest.py` with explicit scope;
- use `marks` and parametrization for CI-grade filtering.

### Feedback checklist

- technical correctness;
- practical relevance;
- clear terminology;
- ability to explain trade-offs;
- structured communication under pressure.

## Q&A

**Q1: What is strongest signal in mock interview?**  
Candidate ties design choices to real delivery constraints, not only theory.

**Q2: What common mistake with HTTP errors appears often?**  
Confusing `401` and `403`, and using incorrect delete/create status semantics.

**Q3: How to improve after each session?**  
Capture weak topics and rehearse with one concrete production example per topic.

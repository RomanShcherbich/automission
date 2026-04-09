# gRPC Card Payment Service Test Design

## What

A practical guide for testing unary gRPC payment endpoint `Pay` in `payment.CardPaymentService`.
Focus areas: proto contract validation, request/response semantics, and gRPC status handling.

## Why

gRPC APIs are contract-driven (`.proto`) and binary on the wire.
Without explicit test design, teams miss compatibility issues, enum handling mistakes, and error-path regressions.

## How

### 1) Contract-first checks

- validate request fields: `card_token`, `amount`, `currency`, `order_id`;
- verify enum response mapping: `SUCCESS`, `DECLINED`, `ERROR`;
- track backward compatibility when evolving field numbers and messages.

### 2) Transport-level expectations

- gRPC uses HTTP/2 with path format `/{package}.{Service}/{Method}`;
- content type is `application/grpc`;
- timeout and auth are passed via metadata;
- failures return gRPC status in trailers (`grpc-status`, `grpc-message`).

### 3) Core functional scenarios

1. Successful payment:
   - valid token/currency/amount/order;
   - expect `status=SUCCESS`, non-empty `transaction_id`.
2. Bank decline:
   - valid format, business rejection;
   - expect `status=DECLINED`, reason in message when available.
3. Invalid input:
   - malformed amount or missing token;
   - expect `INVALID_ARGUMENT` and clear validation message.
4. Duplicate `order_id`:
   - idempotent replay;
   - expect same transaction outcome, no duplicate charge.

### 4) Error-code matrix (minimum)

- `INVALID_ARGUMENT` (3)
- `UNAUTHENTICATED` (16)
- `PERMISSION_DENIED` (7)
- `RESOURCE_EXHAUSTED` (8)
- `INTERNAL` (13)
- `UNAVAILABLE` (14)
- `DEADLINE_EXCEEDED` (4)

### 5) Example client-side assertion flow (Python)

```python
try:
    response = stub.Pay(request, metadata=[("authorization", f"Bearer {token}")])
    assert response.status.name in {"SUCCESS", "DECLINED", "ERROR"}
except grpc.RpcError as e:
    assert e.code() in {
        grpc.StatusCode.INVALID_ARGUMENT,
        grpc.StatusCode.UNAUTHENTICATED,
        grpc.StatusCode.UNAVAILABLE,
    }
```

## Q&A

**Q1: What is primary source of truth in gRPC API tests?**  
The `.proto` contract and its versioned evolution rules.

**Q2: Should we validate only response body status field?**  
No. Also validate gRPC transport status codes and metadata/trailers.

**Q3: Why test idempotency explicitly?**  
Payments are high-risk flows; duplicate charge prevention must be provable.

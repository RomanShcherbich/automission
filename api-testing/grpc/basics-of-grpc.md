# API Documentation: Card Payment Service

## Overview

The service processes card payments using tokenized card data.

- **Transport:** gRPC (HTTP/2)
- **Serialization:** Protocol Buffers
- **Service path:** `payment.CardPaymentService`

---

## Contract: `.proto`

In gRPC, the API contract is defined by the `.proto` file.
Client and server code are generated from this contract, and testing/integration should always rely on the contract version from the service repository.

```proto
syntax = "proto3";

package payment;

service CardPaymentService {
  rpc Pay (CardPaymentRequest) returns (CardPaymentResponse);
}

message CardPaymentRequest {
  string card_token = 1;   // tokenized card reference, not PAN
  string amount = 2;       // amount, e.g. "99.99"
  string currency = 3;     // "RUB", "USD"
  string order_id = 4;     // order identifier
}

message CardPaymentResponse {
  string transaction_id = 1;
  enum Status {
    STATUS_UNSPECIFIED = 0;
    SUCCESS = 1;
    DECLINED = 2;
    ERROR = 3;
  }
  Status status = 2;
  string message = 3;      // error description or empty
}
```

Python code generation:

```bash
python -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. payment.proto
```

---

## What Structures `.proto` Supports

### Scalar field types

`double`, `float`, `int32`, `int64`, `uint32`, `uint64`, `sint32`, `sint64`, `fixed32`, `fixed64`, `sfixed32`, `sfixed64`, `bool`, `string`, `bytes`.

A scalar type stores a single primitive value (not a nested structure and not a collection).

### Composite constructs

| Construct  | Description                            | Example                                             |
| ---------- | -------------------------------------- | --------------------------------------------------- |
| `message`  | Structured record with numbered fields | `message CardPaymentRequest { ... }`                |
| `enum`     | Named constants with numeric values    | `enum Status { SUCCESS = 1; ... }`                  |
| `repeated` | List-like field (many values)          | `repeated string tags = 5;`                         |
| `map`      | Key-value mapping                      | `map<string, int32> scores = 6;`                    |
| `oneof`    | Mutually exclusive fields              | `oneof payload { string text = 1; bytes raw = 2; }` |
| `optional` | Explicitly optional field (proto3)     | `optional string note = 7;`                         |

### Services and RPC

| Construct | Description                                                             |
| --------- | ----------------------------------------------------------------------- |
| `service` | Service definition with RPC methods                                     |
| `rpc`     | Method signature request -> response (supports streaming with `stream`) |

Additional capabilities include imports, package declarations, options, and `google.protobuf` types such as `Timestamp`, `Duration`, wrappers, and `Any`.

---

## Example gRPC Request and How It Is Parsed

gRPC runs over HTTP/2. The request body is binary protobuf by default, so raw traffic is not human-readable like plain HTTP/JSON.

### Wire-level readable equivalent

```text
:method = POST
:path = /payment.CardPaymentService/Pay
:scheme = http
content-type = application/grpc
te = trailers
authorization = Bearer <token>
grpc-timeout = 15S

[body: 1-byte compression flag + 4-byte message length (big-endian) + serialized CardPaymentRequest]
```

### 1-byte compression flag

| Byte   | Numeric value | Meaning                                             |
| ------ | ------------- | --------------------------------------------------- |
| `0x00` | 0             | Not compressed                                      |
| `0x01` | 1             | Compressed payload (for example gzip if negotiated) |

### Human-readable request body

Field view:

```text
CardPaymentRequest:
  card_token = "tok_abc123"
  amount     = "100.00"
  currency   = "RUB"
  order_id   = "ord_789"
```

JSON view (for debugging tools):

```json
{
  "card_token": "tok_abc123",
  "amount": "100.00",
  "currency": "RUB",
  "order_id": "ord_789"
}
```

### Parsing flow

1. Parse HTTP/2 headers and extract RPC path (`/payment.CardPaymentService/Pay`).
2. Read body framing: compression flag, length, payload bytes.
3. Deserialize payload as `CardPaymentRequest`.
4. Invoke service method handler with typed request object.

---

## RPC: `Pay`

Unary call (one request, one response). Charges requested amount and returns payment result.

- **Fully qualified method:** `payment.CardPaymentService/Pay`

### Request: `CardPaymentRequest`

Field numbers are critical in protobuf because binary payload carries field tags, not field names.
Changing existing field numbers breaks compatibility.

| Field        | Type   | Number | Required | Description                                     |
| ------------ | ------ | ------ | -------- | ----------------------------------------------- |
| `card_token` | string | 1      | Yes      | Tokenized card reference                        |
| `amount`     | string | 2      | Yes      | Decimal amount string, e.g. `"99.99"`           |
| `currency`   | string | 3      | Yes      | ISO 4217 currency code                          |
| `order_id`   | string | 4      | Yes      | Merchant order identifier, used for idempotency |

Request example:

```json
{
  "card_token": "tok_abc123",
  "amount": "100.00",
  "currency": "RUB",
  "order_id": "ord_789"
}
```

### Response: `CardPaymentResponse`

| Field            | Type   | Number | Description                                           |
| ---------------- | ------ | ------ | ----------------------------------------------------- |
| `transaction_id` | string | 1      | Payment transaction identifier                        |
| `status`         | enum   | 2      | Final operation status                                |
| `message`        | string | 3      | Human-readable description (usually empty on success) |

Status enum values:

| Status               | Number | Meaning                     |
| -------------------- | ------ | --------------------------- |
| `STATUS_UNSPECIFIED` | 0      | Not set                     |
| `SUCCESS`            | 1      | Payment completed           |
| `DECLINED`           | 2      | Bank rejected payment       |
| `ERROR`              | 3      | Technical or internal error |

Success example:

```json
{
  "transaction_id": "tx_a1b2c3d4",
  "status": "SUCCESS",
  "message": ""
}
```

Declined example:

```json
{
  "transaction_id": "",
  "status": "DECLINED",
  "message": "Insufficient funds"
}
```

---

## gRPC Errors (Status Codes)

Besides business status in response body, RPC can fail with transport-level gRPC status.

| gRPC Code             | Number | Typical reason                  | Client action                   |
| --------------------- | ------ | ------------------------------- | ------------------------------- |
| `OK`                  | 0      | Success                         | Parse response body             |
| `INVALID_ARGUMENT`    | 3      | Validation failed               | Fix request payload             |
| `UNAUTHENTICATED`     | 16     | Invalid/missing auth token      | Refresh/provide token           |
| `PERMISSION_DENIED`   | 7      | Authenticated but forbidden     | Check permissions               |
| `NOT_FOUND`           | 5      | Token/resource not found        | Recreate resource / relink card |
| `RESOURCE_EXHAUSTED`  | 8      | Rate limit exceeded             | Retry with backoff              |
| `FAILED_PRECONDITION` | 9      | Business precondition failed    | Correct domain state            |
| `INTERNAL`            | 13     | Internal server error           | Retry + escalate if persistent  |
| `UNAVAILABLE`         | 14     | Service temporarily unavailable | Retry with backoff              |
| `DEADLINE_EXCEEDED`   | 4      | Request timeout                 | Retry and verify final state    |

### Error response via trailers

```text
HTTP/2 200
content-type: application/grpc

trailer:
  grpc-status: 3
  grpc-message: invalid amount: must be positive
  grpc-status-details-bin: <base64-encoded details>
```

Python client-side handling:

```python
try:
    response = stub.Pay(request, metadata=[("authorization", f"Bearer {token}")])
except grpc.RpcError as e:
    print(e.code())     # e.g. grpc.StatusCode.INVALID_ARGUMENT
    print(e.details())  # e.g. "invalid amount: must be positive"
```

---

## Scenario Examples

### Successful payment

- Valid token, amount, currency, unique order ID
- Expect: `SUCCESS`, non-empty `transaction_id`

### Duplicate order ID (idempotency)

- Same `order_id` as already processed request
- Expect deterministic replay behavior, no duplicate charge

### Bank decline

- Valid payload, banking decision is rejection
- Expect: `DECLINED` with reason when available

### Invalid payload

- Example: negative amount or empty token
- Expect `INVALID_ARGUMENT` and validation details

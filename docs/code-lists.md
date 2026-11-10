# Code lists

## Document types (`doc_type`)

| Code | Meaning | Message |
|---|---|---|
| `220` | Order | ORDERS |
| `230` | Order change | ORDCHG |
| `231` | Order response | ORDRSP |
| `351` | Despatch advice (delivery note) | DESADV |
| `380` | Invoice | INVOIC |
| `381` | Credit note | INVOIC |

## Message function (`doc_function`)

| Code | Meaning | Message |
|---|---|---|
| `9` | Original | ORDERS, DESADV |
| `1` | Cancellation | ORDCHG |
| `29` | Accepted without amendment | ORDRSP |
| `27` | Not accepted | ORDRSP |

Code `4` (change) is not supported in ORDRSP.

## Party qualifiers (`party_qualifier`)

| Code | Party |
|---|---|
| `SU` | Supplier |
| `BY` | Buyer |
| `DP` | Delivery point (cross-dock) |
| `IV` | Invoicee |

## Other codes

| Element | Code | Meaning |
|---|---|---|
| `delivery_type` | `200` | Delivery date and time mean the latest dispatch from the Supplier's warehouse |
| `transport_mode` | `D` | Transport to the cross-dock arranged by the Supplier |
| `transport_mode` | `O` | Transport arranged by the Purchaser (not used in the cross-dock model) |
| `delivery_instructions` | `SC` | Ship complete order |
| `payment_means` (ORDERS) | `42` | Order paid in advance by the end customer |
| `payment_means` (ORDERS) | `72` | Cash on delivery |
| `payment_means` (INVOIC) | `42` | Payment to bank account |
| `quantity_type` | `21` | Ordered quantity |
| `unit` | `PCE` | Pieces |
| `reverse_charge` | `Y` / `N` | Reverse charge applies / does not apply |
| `test_flag` | `0` / `1` | Production / test message |

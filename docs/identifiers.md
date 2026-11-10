# Identifiers and formats

## Pairing messages with the order

Every order has one identifier assigned by MPI: the **MPI order number**. It is sent in ORDERS in `message_id` and `doc_number`. All your messages must return it, otherwise they cannot be paired with the order.

| Message | Where the MPI order number goes |
|---|---|
| ORDRSP | `order_number` in the message header |
| DESADV | `order_number` on every article line |
| INVOIC | `order_number` on every invoice line |
| ORDCHG (from the Purchaser) | `doc_number` – the order being cancelled |

Your own order number is sent once, in `doc_number` of ORDRSP. MPI stores it as the Supplier order number.

Your document numbers (`message_id`, delivery note number, invoice number) are yours to assign. They must be unique.

## GLN numbers of the Purchaser

| Purpose | GLN |
|---|---|
| EDI mailbox of getbigger s.r.o. (sender of ORDERS and ORDCHG, receiver of your messages) | 8593085153508 |
| Delivery point (party DP) – cross-dock, Cieszyńska 11, 43-419 Hażlach, Poland | 8593085153508 |
| Buyer (party BY) and invoicee (party IV) | 8593085153508 |

## Products

| Element | Meaning |
|---|---|
| `article_gtin` | GTIN (EAN) of the product |
| `article_id_supplier` | Your product number (SKU) |
| `article_id_buyer` | The Purchaser's product number |

Use the same GTIN and SKU in DESADV and INVOIC as received in ORDERS.

## Data formats

| Data | Format | Example |
|---|---|---|
| Date | `YYYY-MM-DD` | `2026-11-24` |
| Time | `HH:MM:SS`, 24-hour, local time in Prague (CET/CEST) | `09:15:02` |
| Decimal number | Decimal point, no thousands separator | `1768.90` |
| Quantity | Decimal number, unit `PCE` | `2.000` |
| Currency | ISO 4217 code | `EUR` |
| Country | ISO 3166-1 alpha-2 code | `PL` |
| Character set | UTF-8 | |

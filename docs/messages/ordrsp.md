# ORDRSP – order response

**Direction:** Supplier → Purchaser  
**EANCOM:** ORDRSP D.01B, document type `231`

With ORDRSP you accept or reject an order received in ORDERS. Send one ORDRSP for each order.

## Rules

1. **One response per order.** Every ORDRSP refers to exactly one order.
2. **Pairing.** `order_number` must contain the MPI order number from `doc_number` of the ORDERS message.
3. **Your order number.** `doc_number` is mandatory and contains your internal order number. MPI stores it as the Supplier order number.
4. **Accept or reject the whole order.** `doc_function` = `29` accepts the order: every line will be shipped in full. `doc_function` = `27` rejects the order. Changes (`4`) are not supported.
5. **Rejection.** If at least one product cannot be picked or shipped in full, reject the whole order with `27`. The Purchaser then handles the order with the end customer.
6. **Lines.** `line_items` may be sent but are not required. Lines are not evaluated.

## Elements

| Element | Req. | Value | Description | EANCOM |
|---|---|---|---|---|
| `message_type` | M | `ORDRSP` | Message type | UNH |
| `version` | M | `3.0.7` | Version of the message layout | – |
| `message_id` | M | | Your unique message number | UNH |
| `creation_date` / `creation_time` | M | date / time | Message creation | UNB |
| `receiver` | M | GLN | Purchaser's EDI mailbox | UNB |
| `sender` | M | GLN | Your EDI mailbox | UNB |
| `test_flag` | M | `0` / `1` | `0` production, `1` test | UNB |
| `doc_type` | M | `231` | Order response | BGM |
| `doc_number` | M | | Your order number | BGM |
| `doc_function` | M | `29` / `27` | `29` accepted, `27` not accepted | BGM |
| `doc_date_of_issue` / `doc_time_of_issue` | M | date / time | Acceptance or rejection | DTM+137 |
| `order_number` | M | | MPI order number from ORDERS `doc_number` | RFF+ON |
| `party` `SU` | M | GLN | Supplier GLN | NAD+SU |

## Example – order accepted

```xml title="ORDRSP – accepted"
<orion_message>
    <header>
        <message_type>ORDRSP</message_type>                         <!-- always "ORDRSP" -->
        <version>3.0.7</version>                                    <!-- version of the message layout -->
        <message_id>51432998</message_id>                           <!-- Supplier's unique message number -->
        <creation_date>2026-11-24</creation_date>                   <!-- message creation date -->
        <creation_time>09:40:12</creation_time>                     <!-- message creation time -->
        <receiver>8593085153508</receiver>                          <!-- GLN of the Purchaser's EDI mailbox -->
        <sender>5901234000017</sender>                              <!-- GLN of the Supplier's EDI mailbox -->
        <test_flag>0</test_flag>                                    <!-- 0 = production, 1 = test -->
    </header>
    <body>
        <message_header>
            <doc_type>231</doc_type>                                <!-- always "231" = order response -->
            <doc_number>SO-2026-004512</doc_number>                 <!-- Supplier's order number (mandatory) -->
            <doc_function>29</doc_function>                         <!-- 29 = accepted, 27 = not accepted -->
            <doc_date_of_issue>2026-11-24</doc_date_of_issue>       <!-- date of acceptance / rejection -->
            <doc_time_of_issue>09:40:10</doc_time_of_issue>         <!-- time of acceptance / rejection -->
            <order_number>1190076</order_number>                    <!-- MPI order number from ORDERS <doc_number> -->
            <party>
                <party_qualifier>SU</party_qualifier>               <!-- Supplier -->
                <party_ean>5901234000017</party_ean>                <!-- Supplier GLN -->
            </party>
        </message_header>
        <line_items/>                                               <!-- optional, not evaluated -->
        <summary>
            <number_of_items>0</number_of_items>
        </summary>
    </body>
</orion_message>
```

## Example – order rejected

Identical to the accepted response, except:

```xml
            <doc_function>27</doc_function>                         <!-- 27 = not accepted -->
```

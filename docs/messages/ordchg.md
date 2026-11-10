# ORDCHG – order cancellation

**Direction:** Purchaser → Supplier  
**EANCOM:** ORDCHG D.01B, document type `230`

With ORDCHG the Purchaser cancels an order sent earlier in ORDERS. The Purchaser always cancels the whole order. Changes of quantities or products are not sent.

## Rules

1. **Pairing.** `doc_number` contains the MPI order number of the cancelled order, the same value as `doc_number` in the original ORDERS.
2. **Cancellation reason.** `text_1` contains the reason for the cancellation, for information only.
3. **Before dispatch.** If the order has not left your warehouse yet, do not ship it. No response message is required.
4. **After dispatch.** If the order has already been dispatched, inform your contact person at the Purchaser by e-mail immediately. Do not send ORDRSP.
5. **Lines.** The message repeats the lines of the original order for information.

## Elements

| Element | Req. | Value | Description | EANCOM |
|---|---|---|---|---|
| `message_type` | M | `ORDCHG` | Message type | UNH |
| `message_id` | M | | Unique message number assigned by MPI | UNH |
| `creation_date` / `creation_time` | M | date / time | Message creation | UNB |
| `receiver` / `sender` | M | GLN | Supplier's / Purchaser's EDI mailbox | UNB |
| `test_flag` | M | `0` / `1` | `0` production, `1` test | UNB |
| `doc_type` | M | `230` | Order change | BGM |
| `doc_number` | M | | MPI order number of the cancelled order | BGM |
| `doc_function` | M | `1` | Cancellation | BGM |
| `doc_date_of_issue` / `doc_time_of_issue` | M | date / time | Cancellation | DTM+137 |
| `party` `SU`, `BY` | M | GLN | Supplier, Buyer | NAD |
| `line_items` | M | | Lines of the cancelled order | LIN |
| `text_1` | O | | Cancellation reason | FTX |

## Example

Cancellation of order 1190076.

```xml title="ORDCHG"
<orion_message>
    <header>
        <message_type>ORDCHG</message_type>                         <!-- always "ORDCHG" -->
        <message_id>1190076</message_id>                            <!-- MPI message number -->
        <creation_date>2026-11-24</creation_date>                   <!-- message creation date -->
        <creation_time>10:05:33</creation_time>                     <!-- message creation time -->
        <receiver>5901234000017</receiver>                          <!-- GLN of the Supplier's EDI mailbox -->
        <sender>8593085153508</sender>                              <!-- GLN of the Purchaser's EDI mailbox -->
        <test_flag>0</test_flag>                                    <!-- 0 = production, 1 = test -->
    </header>
    <body>
        <doc_header>
            <doc_type>230</doc_type>                                <!-- always "230" = order change -->
            <doc_number>1190076</doc_number>                        <!-- MPI order number of the cancelled order -->
            <doc_function>1</doc_function>                          <!-- always "1" = cancellation -->
            <doc_date_of_issue>2026-11-24</doc_date_of_issue>       <!-- cancellation date -->
            <doc_time_of_issue>10:05:31</doc_time_of_issue>         <!-- cancellation time -->
            <party>
                <party_qualifier>SU</party_qualifier>               <!-- Supplier -->
                <party_ean>5901234000017</party_ean>                <!-- Supplier GLN -->
                <party_name>SUPPLIER SP. Z O.O.</party_name>
            </party>
            <party>
                <party_qualifier>BY</party_qualifier>               <!-- Buyer -->
                <party_ean>8593085153508</party_ean>                <!-- Buyer GLN -->
                <party_name>getbigger s.r.o.</party_name>
            </party>
        </doc_header>
        <line_items>                                                <!-- lines of the cancelled order -->
            <item>
                <item_number>1</item_number>
                <article_gtin>4015671603999</article_gtin>
                <article_id_supplier>18038</article_id_supplier>
                <article_id_buyer>18038</article_id_buyer>
                <quantity>1.00</quantity>
                <quantity_type>21</quantity_type>
                <unit>PCE</unit>
                <article_name>Hydraulic jack GRH 3/470</article_name>
                <gross_price>72.50</gross_price>
                <net_price>72.50</net_price>
                <total_price>72.50</total_price>
                <vat_rate>0.00</vat_rate>
            </item>
        </line_items>
        <line_texts>
            <text>
                <text_number>1</text_number>
                <text_1>Cancelled by the end customer before dispatch</text_1> <!-- cancellation reason -->
            </text>
        </line_texts>
        <summary>
            <price_ordered>72.50</price_ordered>                    <!-- value of the cancelled order -->
            <items_price>72.50</items_price>                        <!-- sum of total_price -->
            <number_of_items>1</number_of_items>                    <!-- number of lines -->
            <number_of_texts>1</number_of_texts>                    <!-- number of text blocks -->
        </summary>
    </body>
</orion_message>
```

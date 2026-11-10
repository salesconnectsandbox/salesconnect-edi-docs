# DESADV – despatch advice

**Direction:** Supplier → Purchaser  
**EANCOM:** DESADV D.01B, document type `351`

DESADV is your delivery note for a shipment to the cross-dock. It is mandatory. On receipt, MPI switches every order listed in it to status **SHIPPING**, which under the cross-dock model means that the order is on its way to the cross-dock. The cross-dock checks the received goods against the DESADV.

## Rules

1. **One DESADV per shipment.** Send one DESADV for every shipment you hand over to your carrier for the cross-dock. A shipment is either:
    - a **consolidated shipment** (for example a pallet or a large carton) containing several orders, or
    - a **single parcel** containing one order.
2. **All orders of the shipment.** List every order in the shipment. Every order is a separate package (`item` with its own `package_number`) and must contain all its lines in the ordered quantities.
3. **Tracking of the shipment.** `line_texts` carries the tracking number (`text_1`) and the tracking URL (`text_2`) of the shipment to the cross-dock. In a consolidated shipment, all orders share the tracking of the consolidated shipment.
4. **Pairing.** Every `article` must contain `order_number` with the MPI order number from ORDERS `doc_number`.
5. **Timing.** Send DESADV on the day the shipment leaves your warehouse, and only for orders you accepted with ORDRSP `29`.
6. **Dispatch date.** `doc_date_of_issue` is the date the shipment left your warehouse. MPI uses it as the date of the switch to SHIPPING.

## Elements

| Element | Req. | Value | Description | EANCOM |
|---|---|---|---|---|
| `message_type` | M | `DESADV` | Message type | UNH |
| `version` | M | `3.1.1` | Version of the message layout | – |
| `message_id` | M | | Your unique message number | UNH |
| `creation_date` / `creation_time` | M | date / time | Message creation | UNB |
| `receiver` / `sender` | M | GLN | Purchaser's / your EDI mailbox | UNB |
| `test_flag` | M | `0` / `1` | `0` production, `1` test | UNB |
| `doc_number` | M | | Delivery note number | BGM |
| `doc_type` | M | `351` | Despatch advice | BGM |
| `doc_function` | M | `9` | Original | BGM |
| `doc_date_of_issue` | M | date | Dispatch date of the shipment | DTM+137 |
| `party` `SU` | M | GLN | Supplier GLN | NAD+SU |
| `package_number` | M | 1, 2, … | Package sequence number, one package per order | CPS |
| `package_reference` | O | | Package reference | PAC |
| `item_number` | M | 1, 2, … | Line number within the package | LIN |
| `article_gtin` | M | GTIN | Product EAN as in ORDERS | LIN (SRV) |
| `article_id_supplier` | M | | Your SKU as in ORDERS | PIA (SA) |
| `item_type` | O | `Z` | Item type | – |
| `article_name` | O | | Product name | IMD |
| `quantity` | M | decimal | Despatched quantity | QTY+12 |
| `unit` | M | `PCE` | Pieces | QTY |
| `number_of_units` | M | decimal | Number of units | QTY |
| `order_number` | M | | MPI order number from ORDERS `doc_number` | RFF+ON |
| `text_1` | M | | Tracking number of the shipment to the cross-dock | FTX |
| `text_2` | M | URL | Tracking URL of the shipment to the cross-dock | FTX |
| `number_of_items` | M | | Number of article lines in the message | CNT |
| `number_of_texts` | M | | Number of text blocks | – |

## Example – consolidated shipment with several orders

Delivery note DL-2026-11-0187: one pallet with orders 1190076, 1190077 and 1190078, sent to the cross-dock with the Supplier's carrier under tracking number 6012345678901.

```xml title="DESADV – consolidated shipment"
<orion_message>
    <header>
        <message_type>DESADV</message_type>                         <!-- always "DESADV" -->
        <version>3.1.1</version>                                    <!-- version of the message layout -->
        <message_id>51422678</message_id>                           <!-- Supplier's unique message number -->
        <creation_date>2026-11-25</creation_date>                   <!-- message creation date -->
        <creation_time>14:05:20</creation_time>                     <!-- message creation time -->
        <receiver>8593085153508</receiver>                          <!-- GLN of the Purchaser's EDI mailbox -->
        <sender>5901234000017</sender>                              <!-- GLN of the Supplier's EDI mailbox -->
        <test_flag>0</test_flag>                                    <!-- 0 = production, 1 = test -->
    </header>
    <body>
        <message_header>
            <doc_number>DL-2026-11-0187</doc_number>                <!-- delivery note number -->
            <doc_type>351</doc_type>                                <!-- always "351" = despatch advice -->
            <doc_function>9</doc_function>                          <!-- always "9" = original -->
            <doc_date_of_issue>2026-11-25</doc_date_of_issue>       <!-- dispatch date, used for the switch to SHIPPING -->
            <party>
                <party_qualifier>SU</party_qualifier>               <!-- Supplier -->
                <party_ean>5901234000017</party_ean>                <!-- Supplier GLN -->
            </party>
        </message_header>
        <line_items>
            <item>                                                  <!-- package 1 = order 1190076 -->
                <package_number>1</package_number>
                <package_reference>1E</package_reference>
                <articles>
                    <article>
                        <item_number>1</item_number>
                        <article_gtin>4015671603999</article_gtin>
                        <article_id_supplier>18038</article_id_supplier>
                        <item_type>Z</item_type>
                        <article_name>Hydraulic jack GRH 3/470</article_name>
                        <quantity>1.000</quantity>
                        <unit>PCE</unit>
                        <number_of_units>1.000</number_of_units>
                        <order_number>1190076</order_number>        <!-- MPI order number from ORDERS <doc_number> -->
                    </article>
                </articles>
            </item>
            <item>                                                  <!-- package 2 = order 1190077 -->
                <package_number>2</package_number>
                <package_reference>1E</package_reference>
                <articles>
                    <article>
                        <item_number>1</item_number>
                        <article_gtin>5901234123457</article_gtin>
                        <article_id_supplier>20417</article_id_supplier>
                        <item_type>Z</item_type>
                        <article_name>Workshop creeper WC-100</article_name>
                        <quantity>2.000</quantity>
                        <unit>PCE</unit>
                        <number_of_units>2.000</number_of_units>
                        <order_number>1190077</order_number>
                    </article>
                </articles>
            </item>
            <item>                                                  <!-- package 3 = order 1190078 -->
                <package_number>3</package_number>
                <package_reference>1E</package_reference>
                <articles>
                    <article>
                        <item_number>1</item_number>
                        <article_gtin>4015671603999</article_gtin>
                        <article_id_supplier>18038</article_id_supplier>
                        <item_type>Z</item_type>
                        <article_name>Hydraulic jack GRH 3/470</article_name>
                        <quantity>1.000</quantity>
                        <unit>PCE</unit>
                        <number_of_units>1.000</number_of_units>
                        <order_number>1190078</order_number>
                    </article>
                </articles>
            </item>
        </line_items>
        <line_texts>
            <text>
                <text_number>1</text_number>
                <text_1>6012345678901</text_1>                      <!-- tracking number of the shipment to the cross-dock -->
                <text_2>https://tracking.carrier.example/6012345678901</text_2> <!-- tracking URL of the shipment -->
            </text>
        </line_texts>
        <summary>
            <number_of_items>3</number_of_items>                    <!-- number of article lines -->
            <number_of_texts>1</number_of_texts>                    <!-- number of text blocks -->
        </summary>
    </body>
</orion_message>
```

## Example – single parcel

A single parcel with one order uses the same structure: one `item` (package) for the order and the parcel's own tracking number and URL in `text_1` and `text_2`.

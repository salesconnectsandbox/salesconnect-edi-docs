# ORDERS – purchase order

**Direction:** Purchaser → Supplier  
**EANCOM:** ORDERS D.01B, document type `220`

MPI sends one ORDERS message for every order. One message always contains exactly one order. The delivery address (party `DP`) is always the cross-dock; the end customer's address is printed only on the shipping label.

## Rules

1. **Ship complete.** `delivery_instructions` = `SC` means the order must be shipped as a whole. If you cannot ship every line in full, reject the order with ORDRSP `27`.
2. **Dispatch deadline (SLA).** `delivery_date` and `delivery_time` are the key SLA parameter: by this date and time the order must be packed, labelled and ready for pick-up.
3. **Transport mode.** `transport_mode` = `D`: you arrange and pay the transport of the order to the cross-dock.
4. **Shipping label.** `text_1` contains the URL of the last-mile shipping label for the end customer (PDF), `text_2` its tracking number and `text_3` the tracking URL. Attach the label to the parcel when you deliver several orders in one consolidated shipment. Parcels sent individually are relabelled at the cross-dock.
5. **Transport line.** The first line of the order can be a transport line with `article_id_supplier` = `DELIVERY` and a non-existent EAN. It is not a product: do not pick it, do not confirm it as stock and do not invoice it.
6. **Prices.** Prices are B2B prices per unit agreed with the Purchaser, excluding VAT, in the order currency.

## Elements

### Header

| Element | Req. | Value | Description | EANCOM |
|---|---|---|---|---|
| `message_type` | M | `ORDERS` | Message type | UNH |
| `version` | M | `3.0.5` | Version of the message layout | – |
| `message_id` | M | | MPI order number | UNH |
| `creation_date` / `creation_time` | M | date / time | Message creation | UNB |
| `receiver` | M | GLN | Supplier's EDI mailbox | UNB |
| `sender` | M | GLN | Purchaser's EDI mailbox | UNB |
| `test_flag` | M | `0` / `1` | `0` production, `1` test | UNB |

### Document header

| Element | Req. | Value | Description | EANCOM |
|---|---|---|---|---|
| `doc_type` | M | `220` | Order | BGM |
| `doc_number` | M | | MPI order number. Return it as `order_number` in ORDRSP, DESADV and INVOIC. | BGM |
| `doc_function` | M | `9` | Original | BGM |
| `doc_date_of_issue` / `doc_time_of_issue` | M | date / time | Order confirmed on the sales channel (payment confirmed) | DTM+137 |
| `delivery_date` / `delivery_time` | M | date / time | SLA deadline: order packed, labelled and ready for pick-up | DTM+200 |
| `delivery_type` | M | `200` | The delivery date is the dispatch (pick-up) date | DTM+200 |
| `transport_mode` | M | `D` | Transport to the cross-dock arranged by the Supplier | – |
| `currency_code` | M | `EUR` | Order currency | CUX |
| `delivery_instructions` | M | `SC` | Ship complete order | – |
| `payment_means` | M | `42` | `42` order paid in advance by the end customer, `72` cash on delivery | PAI |

### Parties

| Qualifier | Party | Content |
|---|---|---|
| `SU` | Supplier | `party_ean` (GLN), `party_name` |
| `BY` | Buyer – getbigger s.r.o. | `party_ean` (GLN), `party_name` |
| `DP` | Delivery point – cross-dock | `party_ean`, name, address, contact |
| `IV` | Invoicee – getbigger s.r.o. | `party_ean`, name, address, `company_id` (company ID), `tax_id` (VAT number), contact |

EANCOM: NAD+SU, NAD+BY, NAD+DP, NAD+IV; VAT number RFF+VA; contacts CTA/COM.

### Lines

| Element | Req. | Value | Description | EANCOM |
|---|---|---|---|---|
| `item_number` | M | 1, 2, … | Line number | LIN |
| `article_gtin` | M | GTIN | Product EAN | LIN (SRV) |
| `article_id_supplier` | M | | Your product number (SKU), or `DELIVERY` for a transport line | PIA (SA) |
| `article_id_buyer` | O | | Purchaser's product number | PIA (IN) |
| `quantity` | M | decimal | Ordered quantity | QTY+21 |
| `quantity_type` | M | `21` | Ordered quantity | QTY+21 |
| `unit` | M | `PCE` | Pieces | QTY |
| `article_name` | M | | Product name | IMD |
| `gross_price` | M | decimal | Unit price before discounts, excl. VAT | PRI+AAB |
| `net_price` | M | decimal | Unit price after discounts, excl. VAT | PRI+AAA |
| `total_price` | M | decimal | `quantity` × `net_price` | MOA+203 |
| `vat_rate` | M | `0.00` | VAT rate in % | TAX+7+VAT |

### Free texts and summary

| Element | Description | EANCOM |
|---|---|---|
| `text_1` | Last-mile shipping label URL (PDF) | FTX |
| `text_2` | Tracking number of the shipping label | FTX |
| `text_3` | Tracking URL | FTX |
| `total_quantity` | Sum of ordered quantities | CNT |
| `items_price` | Sum of `total_price` | MOA |
| `number_of_items` | Number of lines | CNT |
| `number_of_texts` | Number of text blocks | – |

## Example

Order 1190076, one product, delivered to the cross-dock.

```xml title="ORDERS"
<orion_message>
    <header>
        <message_type>ORDERS</message_type>                         <!-- always "ORDERS" -->
        <version>3.0.5</version>                                    <!-- version of the message layout -->
        <message_id>1190076</message_id>                            <!-- MPI order number -->
        <creation_date>2026-11-24</creation_date>                   <!-- message creation date -->
        <creation_time>09:15:02</creation_time>                     <!-- message creation time -->
        <receiver>5901234000017</receiver>                          <!-- GLN of the Supplier's EDI mailbox -->
        <sender>8593085153508</sender>                              <!-- GLN of the Purchaser's EDI mailbox -->
        <test_flag>0</test_flag>                                    <!-- 0 = production, 1 = test -->
    </header>
    <body>
        <doc_header>
            <doc_type>220</doc_type>                                <!-- always "220" = order -->
            <doc_number>1190076</doc_number>                        <!-- MPI order number, return it as <order_number> -->
            <doc_function>9</doc_function>                          <!-- always "9" = original -->
            <doc_date_of_issue>2026-11-24</doc_date_of_issue>       <!-- order confirmed on the sales channel -->
            <doc_time_of_issue>08:58:41</doc_time_of_issue>
            <delivery_date>2026-11-25</delivery_date>               <!-- SLA: order packed, labelled and ready for pick-up -->
            <delivery_time>12:00:00</delivery_time>                 <!-- SLA time -->
            <delivery_type>200</delivery_type>                      <!-- always "200" = delivery date means dispatch date -->
            <transport_mode>D</transport_mode>                      <!-- "D" = transport to the cross-dock arranged by the Supplier -->
            <currency_code>EUR</currency_code>                      <!-- order currency -->
            <delivery_instructions>SC</delivery_instructions>       <!-- always "SC" = ship complete order -->
            <payment_means>42</payment_means>                       <!-- 42 = paid in advance, 72 = cash on delivery -->
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
            <party>
                <party_qualifier>DP</party_qualifier>               <!-- Delivery point = cross-dock -->
                <party_ean>8593085153508</party_ean>                <!-- GLN of the delivery point -->
                <party_name>getbigger s.r.o. – cross-dock</party_name>
                <street>Cieszyńska 11</street>
                <place>Hażlach</place>
                <postcode>43-419</postcode>
                <country_code>PL</country_code>
            </party>
            <party>
                <party_qualifier>IV</party_qualifier>               <!-- Invoicee -->
                <party_ean>8593085153508</party_ean>                <!-- GLN for invoicing -->
                <party_name>getbigger s.r.o.</party_name>
                <street>Haštalská 796/3</street>
                <place>Praha 1</place>
                <postcode>110 00</postcode>
                <country_code>CZ</country_code>
                <company_id>19298285</company_id>                   <!-- company ID (IČO) -->
                <tax_id>CZ19298285</tax_id>                         <!-- VAT number -->
                <contact_email>finance@salesconnect.cz</contact_email>
            </party>
        </doc_header>
        <line_items>
            <item>
                <item_number>1</item_number>                        <!-- line number -->
                <article_gtin>4015671603999</article_gtin>          <!-- product GTIN (EAN) -->
                <article_id_supplier>18038</article_id_supplier>    <!-- Supplier product number (SKU) -->
                <article_id_buyer>18038</article_id_buyer>          <!-- Purchaser product number -->
                <quantity>1.00</quantity>                           <!-- ordered quantity -->
                <quantity_type>21</quantity_type>                   <!-- always "21" = ordered quantity -->
                <unit>PCE</unit>                                    <!-- always "PCE" = pieces -->
                <article_name>Hydraulic jack GRH 3/470</article_name>
                <gross_price>72.50</gross_price>                    <!-- unit price before discounts, excl. VAT -->
                <net_price>72.50</net_price>                        <!-- unit price after discounts, excl. VAT -->
                <total_price>72.50</total_price>                    <!-- quantity * net_price -->
                <vat_rate>0.00</vat_rate>                           <!-- VAT rate in % -->
            </item>
        </line_items>
        <line_texts>
            <text>
                <text_number>1</text_number>
                <text_1>https://s3.eu-central-1.amazonaws.com/salesconnect.cz/1190076.pdf</text_1> <!-- last-mile shipping label URL -->
                <text_2>90387355142</text_2>                        <!-- tracking number -->
                <text_3>https://gls-group.eu/track/90387355142</text_3> <!-- tracking URL -->
            </text>
        </line_texts>
        <summary>
            <total_quantity>1</total_quantity>                      <!-- sum of quantities -->
            <items_price>72.50</items_price>                        <!-- sum of total_price -->
            <number_of_items>1</number_of_items>                    <!-- number of lines -->
            <number_of_texts>1</number_of_texts>                    <!-- number of text blocks -->
        </summary>
    </body>
</orion_message>
```

The other examples in this guide continue with this order and a second order, 1190077 (2 × Workshop creeper WC-100, GTIN 5901234123457, SKU 20417, 14.90 EUR per unit), delivered in the same shipment.

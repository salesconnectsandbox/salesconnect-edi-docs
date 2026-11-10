# INVOIC – invoice and credit note

**Direction:** Supplier → Purchaser  
**EANCOM:** INVOIC D.01B, document type `380` (invoice) or `381` (credit note)

INVOIC carries your invoices and credit notes for the Purchaser. Both use the same structure and differ in `doc_type`.

## Rules for invoices

1. **Only dispatched orders.** Invoice only orders that have been dispatched from the cross-dock (status SHIPPED).
2. **Several orders per invoice.** One invoice may cover several orders. Every line must contain `order_number` with the MPI order number from ORDERS `doc_number`.
3. **Prices.** `net_price` must match the price from the order.
4. **Dates.** `issue_date` must be equal to or later than the dispatch date of the goods. `tax_date` must be equal to or later than `issue_date`. `due_date` must match the payment terms of the cooperation agreement.
5. **VAT.** All invoices are intra-Community supplies with VAT rate `0.00` and `reverse_charge` = `Y`. Your VAT number (`tax_id`) must carry the country prefix.

## Rules for credit notes

1. **Document type.** `doc_type` = `381`.
2. **Reference to the original invoice.** The credit note header must contain the number and the date of the original invoice it corrects. This is mandatory by law and a credit note without it is not accepted. Fill in `reference_invoice_number` and `reference_invoice_date` (in EANCOM: RFF+IV and DTM+171).
3. **One original invoice per credit note.** If corrections relate to several invoices, issue one credit note per original invoice.
4. **Lines.** Every line contains the MPI order number (`order_number`) of the corrected order. Quantities and line totals are negative, unit prices are positive.
5. **Reason.** `reference_text` contains the reason for the correction (for example return, damaged goods, price correction).
6. **Same VAT treatment** as the original invoice: VAT rate `0.00`, `reverse_charge` = `Y`.

## Elements

### Header

| Element | Req. | Value | Description | EANCOM |
|---|---|---|---|---|
| `message_type` | M | `INVOIC` | Message type | UNH |
| `version` | M | `3.0.7` | Version of the message layout | – |
| `message_id` | M | | Your unique message number | UNH |
| `creation_date` / `creation_time` | M | date / time | Message creation | UNB |
| `receiver` / `sender` | M | GLN | Purchaser's / your EDI mailbox | UNB |
| `test_flag` | M | `0` / `1` | `0` production, `1` test | UNB |
| `doc_number` | M | | Invoice or credit note number | BGM |
| `doc_type` | M | `380` / `381` | `380` invoice, `381` credit note | BGM |
| `issue_date` | M | date | Date of issue | DTM+137 |
| `tax_date` | M | date | Date of the taxable supply | DTM+131 |
| `due_date` | M | date | Due date | DTM+13 |
| `payment_means` | M | `42` | Payment to bank account | PAI |
| `currency_code` | M | `EUR` | Invoice currency | CUX |
| `reverse_charge` | M | `Y` | Reverse charge / intra-Community supply | TAX |
| `reference_invoice_number` | C | | Credit note only: number of the original invoice | RFF+IV |
| `reference_invoice_date` | C | date | Credit note only: date of the original invoice | DTM+171 |
| `reference_text` | C | | Credit note only: reason for the correction | FTX |

### Parties

| Qualifier | Party | Content | EANCOM |
|---|---|---|---|
| `BY` | Buyer – getbigger s.r.o. | `party_id` (GLN), `company_id`, `tax_id` | NAD+BY, RFF+VA |
| `SU` | Supplier | `party_id` (GLN), `company_id`, `tax_id` | NAD+SU, RFF+VA |

### Lines

| Element | Req. | Description | EANCOM |
|---|---|---|---|
| `item_number` | M | Line number | LIN |
| `article_id_supplier` | M | Your SKU as in ORDERS | PIA (SA) |
| `quantity` | M | Invoiced quantity (negative on credit notes) | QTY+47 |
| `net_price` | M | Unit price excl. VAT | PRI+AAA |
| `total_price` | M | `quantity` × `net_price` | MOA+203 |
| `vat_rate` | M | VAT rate in % (`0.00`) | TAX+7+VAT |
| `order_number` | M | MPI order number from ORDERS `doc_number` | RFF+ON |

### Summary

| Element | Req. | Description | EANCOM |
|---|---|---|---|
| `number_of_items` | M | Number of lines | CNT |
| `price_invoiced` | M | Total amount including VAT | MOA+77 |
| `vat_group` / `vat_basis` / `vat_rate` / `vat_total` | M | VAT breakdown per rate | TAX, MOA+125, MOA+124 |
| `vat_total` (summary) | M | Total VAT | MOA+124 |

## Example – invoice

Invoice FV-2026/11/0458 for orders 1190076 and 1190077.

```xml title="INVOIC – invoice"
<orion_message>
    <header>
        <message_type>INVOIC</message_type>                         <!-- always "INVOIC" -->
        <version>3.0.7</version>                                    <!-- version of the message layout -->
        <message_id>529947997</message_id>                          <!-- Supplier's unique message number -->
        <creation_date>2026-11-26</creation_date>                   <!-- message creation date -->
        <creation_time>13:16:45</creation_time>                     <!-- message creation time -->
        <receiver>8593085153508</receiver>                          <!-- GLN of the Purchaser's EDI mailbox -->
        <sender>5901234000017</sender>                              <!-- GLN of the Supplier's EDI mailbox -->
        <test_flag>0</test_flag>                                    <!-- 0 = production, 1 = test -->
    </header>
    <body>
        <message_header>
            <doc_number>FV-2026/11/0458</doc_number>                <!-- invoice number -->
            <doc_type>380</doc_type>                                <!-- 380 = invoice, 381 = credit note -->
            <issue_date>2026-11-26</issue_date>                     <!-- date of issue, not earlier than the DESADV dispatch date -->
            <tax_date>2026-11-26</tax_date>                         <!-- date of the taxable supply, not earlier than the issue date -->
            <due_date>2026-12-10</due_date>                         <!-- due date according to the cooperation agreement -->
            <payment_means>42</payment_means>                       <!-- 42 = payment to bank account -->
            <currency_code>EUR</currency_code>                      <!-- invoice currency -->
            <reverse_charge>Y</reverse_charge>                      <!-- always "Y" = intra-Community supply, reverse charge -->
            <party>
                <party_qualifier>BY</party_qualifier>               <!-- Buyer -->
                <party_id>8593085153508</party_id>                  <!-- Buyer GLN -->
                <company_id>19298285</company_id>                   <!-- Buyer company ID -->
                <tax_id>CZ19298285</tax_id>                         <!-- Buyer VAT number -->
            </party>
            <party>
                <party_qualifier>SU</party_qualifier>               <!-- Supplier -->
                <party_id>5901234000017</party_id>                  <!-- Supplier GLN -->
                <company_id>1234563218</company_id>                 <!-- Supplier company ID (NIP) -->
                <tax_id>PL1234563218</tax_id>                       <!-- Supplier VAT number with country prefix -->
            </party>
        </message_header>
        <line_items>
            <item>
                <item_number>1</item_number>
                <article_id_supplier>18038</article_id_supplier>    <!-- Supplier SKU -->
                <quantity>1.000</quantity>                          <!-- invoiced quantity -->
                <net_price>72.50</net_price>                        <!-- unit price excl. VAT -->
                <total_price>72.50</total_price>                    <!-- quantity * net_price -->
                <vat_rate>0.00</vat_rate>                           <!-- VAT rate in % -->
                <order_number>1190076</order_number>                <!-- MPI order number from ORDERS <doc_number> -->
            </item>
            <item>
                <item_number>2</item_number>
                <article_id_supplier>20417</article_id_supplier>
                <quantity>2.000</quantity>
                <net_price>14.90</net_price>
                <total_price>29.80</total_price>
                <vat_rate>0.00</vat_rate>
                <order_number>1190077</order_number>
            </item>
        </line_items>
        <advances/>                                                 <!-- not used -->
        <summary>
            <number_of_items>2</number_of_items>                    <!-- number of lines -->
            <price_invoiced>102.30</price_invoiced>                 <!-- total amount incl. VAT -->
            <tax_details>
                <vat>
                    <vat_group>0</vat_group>                        <!-- VAT group 0 = rate 0 % -->
                    <vat_basis>102.30</vat_basis>                   <!-- taxable amount -->
                    <vat_rate>0.00</vat_rate>                       <!-- VAT rate in % -->
                    <vat_total>0.00</vat_total>                     <!-- VAT amount -->
                </vat>
                <vat_total>0.00</vat_total>                         <!-- total VAT -->
            </tax_details>
        </summary>
    </body>
</orion_message>
```

## Example – credit note

Credit note DB-2026/12/0031 for the return of one of the two units of order 1190077, invoiced on FV-2026/11/0458.

```xml title="INVOIC – credit note"
<orion_message>
    <header>
        <message_type>INVOIC</message_type>
        <version>3.0.7</version>
        <message_id>529951204</message_id>
        <creation_date>2026-12-08</creation_date>
        <creation_time>10:22:07</creation_time>
        <receiver>8593085153508</receiver>
        <sender>5901234000017</sender>
        <test_flag>0</test_flag>
    </header>
    <body>
        <message_header>
            <doc_number>DB-2026/12/0031</doc_number>                <!-- credit note number -->
            <doc_type>381</doc_type>                                <!-- 381 = credit note -->
            <issue_date>2026-12-08</issue_date>
            <tax_date>2026-12-08</tax_date>
            <due_date>2026-12-22</due_date>
            <payment_means>42</payment_means>
            <currency_code>EUR</currency_code>
            <reverse_charge>Y</reverse_charge>
            <reference_invoice_number>FV-2026/11/0458</reference_invoice_number> <!-- number of the original invoice (mandatory) -->
            <reference_invoice_date>2026-11-26</reference_invoice_date>         <!-- date of the original invoice (mandatory) -->
            <reference_text>Return of goods by the end customer</reference_text> <!-- reason for the correction -->
            <party>
                <party_qualifier>BY</party_qualifier>
                <party_id>8593085153508</party_id>
                <company_id>19298285</company_id>
                <tax_id>CZ19298285</tax_id>
            </party>
            <party>
                <party_qualifier>SU</party_qualifier>
                <party_id>5901234000017</party_id>
                <company_id>1234563218</company_id>
                <tax_id>PL1234563218</tax_id>
            </party>
        </message_header>
        <line_items>
            <item>
                <item_number>1</item_number>
                <article_id_supplier>20417</article_id_supplier>
                <quantity>-1.000</quantity>                         <!-- credited quantity, negative -->
                <net_price>14.90</net_price>                        <!-- unit price, positive -->
                <total_price>-14.90</total_price>                   <!-- quantity * net_price, negative -->
                <vat_rate>0.00</vat_rate>
                <order_number>1190077</order_number>                <!-- MPI order number of the corrected order -->
            </item>
        </line_items>
        <advances/>
        <summary>
            <number_of_items>1</number_of_items>
            <price_invoiced>-14.90</price_invoiced>                 <!-- total amount incl. VAT, negative -->
            <tax_details>
                <vat>
                    <vat_group>0</vat_group>
                    <vat_basis>-14.90</vat_basis>
                    <vat_rate>0.00</vat_rate>
                    <vat_total>0.00</vat_total>
                </vat>
                <vat_total>0.00</vat_total>
            </tax_details>
        </summary>
    </body>
</orion_message>
```

# Getting connected

## What we need from you

Send the following to **tech@salesconnect.cz**:

1. **Your EDI service provider** – who provides your EDI mailbox (for example ORiON/GRiT, EDITEL or Comarch EDI) and a technical contact there.
2. **Your GLN** – the GLN of your company as a supplier, and the GLN of your EDI mailbox if it is different.
3. **Your VAT number** and the contact person for EDI matters.

If you do not have a GLN, request it from GS1 Polska before you start.

## What you need

- **An EDI mailbox** with any EDI service provider. You do not need a second account with our provider. Your provider connects to ours through an interconnection.
- **Product master data.** Every product you supply must be agreed with the Purchaser in advance, with its GTIN (EAN) and your own product number (SKU). Orders always contain both.
- **Transport to the cross-dock.** You deliver the orders to the cross-dock with your own transport and at your own cost.
- **The ability to print the last-mile shipping label** from the URL provided in each order (PDF), for consolidated shipments.

## What to tell your EDI provider

Your EDI provider has to set up the connection on its side. Forward the following text to them, together with a link to this guide:

!!! note "Request for your EDI provider"

    Please set up EDI communication with our new trading partner:

    - **Partner:** getbigger s.r.o., Haštalská 796/3, 110 00 Praha 1, Czech Republic, VAT number CZ19298285
    - **Partner GLN (EDI mailbox):** 8593085153508
    - **Partner's EDI service provider:** GRiT s.r.o. (ORiON), Czech Republic – please arrange the interconnection
    - **Standard:** UN/EDIFACT EANCOM®, directory D.01B
    - **Messages:**
        - ORDERS (purchase order) – inbound, from the partner to us
        - ORDRSP (order response) – outbound, from us to the partner
        - ORDCHG (order cancellation) – inbound
        - DESADV (despatch advice) – outbound, from us to the partner
        - INVOIC (invoice 380 and credit note 381) – outbound
    - **Specific requirements:**
        - The first order line can be a transport line with product number `DELIVERY` and a non-existent EAN. It is not a product.
        - The free text of the order contains the URL of the last-mile shipping label, its tracking number and the tracking URL.
        - The order must always be shipped complete (delivery instruction `SC`).
        - DESADV carries the tracking number and tracking URL of the shipment to the partner's cross-dock in its free texts.
    - **Message specification and examples:** this guide (link)
    - **Technical contact at the partner:** tech@salesconnect.cz

## Message formats

The messages are based on EANCOM® D.01B. Your provider may deliver them to you in EANCOM EDIFACT, in the XML representation shown in this guide or in its own format, as long as the content maps to the elements described on the message pages.

**Orders come from us.** The key task on your side is to map all elements of the ORDERS example correctly, including the requirements listed in [ORDERS](messages/orders.md).

## Onboarding steps

1. **Agreement.** A signed cooperation agreement with getbigger s.r.o. is a prerequisite.
2. **Identifiers.** You send the information listed under [What we need from you](#what-we-need-from-you) to tech@salesconnect.cz.
3. **Interconnection.** You forward the request above to your EDI provider. Both EDI providers set up the routing between the mailboxes.
4. **Testing.** The Purchaser sends test orders. You respond with ORDRSP, DESADV and INVOIC test messages. Test messages are marked with `test_flag` = `1`.
5. **Go-live.** After a successful test, both parties switch to production messages (`test_flag` = `0`) from an agreed date.

## Minimum scope for go-live

| Message | Required at go-live |
|---|---|
| ORDERS | Yes |
| ORDRSP | Yes |
| ORDCHG | Yes |
| DESADV | Yes |
| INVOIC | By agreement |

## Go-live checklist

- Your system reads the MPI order number from `doc_number` in ORDERS and returns it in every subsequent message.
- The transport line `DELIVERY` is recognised and not treated as a product.
- An order is always accepted or rejected as a whole.
- Your system can download and print the last-mile label from the URL in the order (consolidated shipments).
- One parcel per order, delivered to the cross-dock address from the order with your own transport.
- DESADV is sent for every shipment to the cross-dock, with all its orders and the tracking number and tracking URL of the shipment.
- Invoices and credit notes contain the MPI order number on every line.
- Credit notes reference the original invoice number and date.
- Dates in `YYYY-MM-DD`, decimal point as the decimal separator.

## Contact

All questions about the EDI connection: **tech@salesconnect.cz**. Questions about the business cooperation are handled by your account manager at getbigger.

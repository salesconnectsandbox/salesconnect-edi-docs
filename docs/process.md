# Order process

## Lifecycle of one order

| Step | Who | Message | Order status in MPI | What happens |
|---|---|---|---|---|
| 1 | Purchaser | ORDERS | CONFIRMED | The order is sent to the Supplier. One message = one order. |
| 2a | Supplier | ORDRSP, `doc_function` = `29` | CONFIRMED | The Supplier accepts the whole order. MPI stores the Supplier's order number. |
| 2b | Supplier | ORDRSP, `doc_function` = `27` | CANCELLED | The Supplier rejects the whole order. The Purchaser contacts the end customer. |
| 3 | Supplier | DESADV | SHIPPING | The order has left the Supplier's warehouse and is on its way to the cross-dock. DESADV carries the tracking of the shipment. |
| 4 | Purchaser | – | SHIPPED | The cross-dock has dispatched the parcel to the end customer. |
| 5 | Supplier | INVOIC 380 | – | The Supplier invoices the order. |
| – | Purchaser | ORDCHG | CANCELLED | The Purchaser cancels the order before dispatch. |

## Rules

**Whole orders only.** An order is accepted, shipped and invoiced as a whole. If any line cannot be shipped in full, reject the whole order with ORDRSP `27`. Partial deliveries, quantity changes and product substitutions are not supported.

**Response time.** Send ORDRSP as soon as possible after receiving the order, and in any case within the deadline agreed in the cooperation agreement.

**Dispatch deadline (SLA).** `delivery_date` and `delivery_time` in ORDERS are the key SLA parameter: by this date and time the order must be packed, labelled and ready for pick-up at your warehouse for transport to the cross-dock.

**One parcel per order.** Pack every order as a separate parcel. Do not combine several orders in one parcel.

**Delivery to the cross-dock.** Deliver all parcels to the cross-dock address given in the order (party `DP`) with your own transport and at your own cost. For every shipment to the cross-dock, send one DESADV listing all orders in it, with the tracking number and tracking URL of the shipment. DESADV is mandatory: without it the orders do not switch to SHIPPING.

**Shipping labels.** The order contains the last-mile shipping label for the end customer (URL in `text_1`).

- **Consolidated shipment** (several orders delivered together, for example on a pallet): attach the last-mile label from the order to each parcel before dispatch.
- **Individual parcels** (each order sent separately with your carrier): send the parcel to the cross-dock with your carrier's label. The cross-dock relabels it with the last-mile label.

**Receipt at the cross-dock.** The cross-dock checks the received parcels against the delivery note. Damaged, missing or surplus goods are reported to your contact person by e-mail and resolved by return or replacement outside EDI.

**Invoicing.** Invoice only orders that have been dispatched from the cross-dock (status SHIPPED). One invoice may cover several orders. Every invoice line must carry the MPI order number.

**Credit notes.** Returns, rejected deliveries and other corrections of an issued invoice are settled with a credit note (INVOIC `381`) referencing the original invoice.

**Cancellation by the Purchaser.** If you receive ORDCHG before dispatch, do not ship the order. No response message is required. If the order has already left your warehouse, inform your contact person by e-mail immediately.

## VAT

All supplies under this model are intra-Community supplies of goods from Poland to getbigger s.r.o. (VAT number CZ19298285). Invoices are issued with VAT rate 0 % and the reverse charge indicator set (`reverse_charge` = `Y`). The VAT treatment follows the cooperation agreement.

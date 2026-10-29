
## Title

Negative Quantity Parameter Manipulation Allows Total Price Reduction and Arbitrary Order Cost Reduction on Online Ordering Application.

## Affected URL

https://upserve.pwnedbox.site/

## Description

A business logic vulnerability was identified in the online ordering (OLO) checkout workflow where item quantities submitted within order JSON payloads accept negative integer values without proper server-side validation or bounds checking. 

An attacker can intercept the HTTP request during checkout and modify an item's `quantity` parameter to a negative value (e.g., changing `1` to `-1`). Consequently, the server subtracts the item's cost from the overall order total instead of adding it, allowing users to bypass payment integrity controls and successfully place orders at a significantly reduced or manipulated total cost.

## Steps to Reproduce

1. Navigate to the target application: `https://upserve.pwnedbox.site/`.
2. Register a new user account and log in.
3. Add multiple menu items to the cart (e.g., BreadPudding and NY Cheesecake).
4. Proceed to checkout and initiate the order placement while intercepting the HTTP request using a proxy tool (e.g., Burp Suite).
5. Locate the JSON payload containing the items array and the `quantity` parameter for a specific item.
6. Modify the `quantity` value of an item from a positive integer to a negative integer (e.g., change `"quantity": 1` to `"quantity": -1`).
7. Forward the request and observe that the order subtotal and total charges are reduced by the negative item's price.
8. Verify that the order is placed successfully and the card on file is charged at the heavily reduced total amount.

## POC
<img width="753" height="737" alt="image" src="https://github.com/user-attachments/assets/2dc34abd-b55d-46c1-b1d9-dad1091f4e29" />
<img width="1147" height="486" alt="image" src="https://github.com/user-attachments/assets/218b4ae9-06cb-4094-948d-e3d86c0482b6" />
<img width="1123" height="568" alt="image" src="https://github.com/user-attachments/assets/3aff8610-dbf7-4aa6-824b-1d2a43e4fee4" />
<img width="1022" height="675" alt="image" src="https://github.com/user-attachments/assets/c1e521a2-2015-4c01-a004-e6f19e45d907" />
<img width="1009" height="648" alt="image" src="https://github.com/user-attachments/assets/1847a2ad-8fcb-4a47-ad47-fe98a128f940" />
<img width="1068" height="540" alt="image" src="https://github.com/user-attachments/assets/61b39d0d-8770-4a2c-87aa-d2be8f1efabc" />



## Impact

1. Financial loss for the platform and restaurant merchant as orders can be placed for a fraction of their actual value by injecting negative quantities.
2. Complete bypass of payment validation logic and order total calculation integrity.
3. Exploitation of business logic flaws to manipulate transaction amounts during checkout.

## Remediation

1. Implement strict server-side validation to ensure that item quantities only accept positive integers greater than zero (`quantity > 0`).
2. Reject any negative or zero values submitted in order placement payloads with an appropriate error response.
3. Recalculate order subtotals, taxes, and final charges securely on the server side using trusted database item prices rather than relying on client-submitted quantity metrics.

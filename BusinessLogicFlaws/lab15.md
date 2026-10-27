## Title

Negative Quantity Parameter Manipulation Leads to Total Price Reduction and Arbitrary Order Cost Reduction on E-Commerce Platform.

## Affected URL

https://controlzone.pwnedbox.site/cart/update

## Description

A business logic vulnerability was identified in the shopping cart quantity management workflow where the application accepts negative integer values for item quantities without proper server-side bounds validation. 

An attacker can intercept the HTTP POST request sent when updating a cart item's quantity and modify the parameter to a negative value (e.g., `-1`). Consequently, the application subtracts the item's cost from the overall order subtotal instead of adding it, enabling attackers to bypass payment integrity controls and purchase items at a significantly reduced or manipulated total cost.

## Steps to Reproduce

1. Navigate to the target application: `https://controlzone.pwnedbox.site/`.
2. Register a new user account and log in.
3. Add multiple products to the shopping cart (e.g., NiTHO V80 Bluetooth Gamepad and Ant Esports MG15 Super Cube Wireless Gamepad).
4. Navigate to the shopping cart page and intercept the HTTP request when updating the quantity of an item using a proxy tool (e.g., Burp Suite).
5. Modify the `quantity` parameter value to a negative integer (e.g., change `1` to `-1`) in the POST body of the `/cart/update` endpoint.
6. Forward the request and observe that the item subtotal becomes negative and reduces the overall order total.
7. Proceed through checkout and place the order to successfully execute the transaction at the manipulated price.

## POC

<img width="1086" height="702" alt="image" src="https://github.com/user-attachments/assets/64f8848e-f886-440a-8efe-369fcfda98e9" />
<img width="1121" height="663" alt="image" src="https://github.com/user-attachments/assets/23fd9db2-a248-49fc-bdd3-0caadd3b264d" />
<img width="1107" height="634" alt="image" src="https://github.com/user-attachments/assets/7506ece5-65ce-47f2-991f-a4856afd052f" />
<img width="710" height="974" alt="image" src="https://github.com/user-attachments/assets/1b4d7612-87fd-4091-b50d-65c939386521" />
<img width="1108" height="646" alt="image" src="https://github.com/user-attachments/assets/0e7ff35e-ffa7-4048-bb23-da292829794f" />
<img width="1110" height="756" alt="image" src="https://github.com/user-attachments/assets/0289653b-67ee-4eb2-9e98-3b9be1def47b" />




## Impact

1. Financial loss for the platform as orders can be placed for a fraction of their actual value by injecting negative quantities.
2. Complete breakdown of shopping cart integrity, subtotal calculations, and inventory tracking logic.
3. Exploitation of business logic flaws to manipulate payment totals during checkout.

## Remediation

1. Implement strict server-side validation to ensure that item quantities can only accept positive integers greater than zero (`quantity > 0`).
2. Reject any negative or zero values submitted via quantity update or cart modification requests with appropriate error responses.
3. Recalculate cart totals, taxes, and shipping securely on the server side using authenticated session states and trusted database values.

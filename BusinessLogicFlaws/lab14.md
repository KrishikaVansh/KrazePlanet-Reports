## Title

Client-Side Price Manipulation Allows Purchasing Products at Arbitrary Low Prices on E-Commerce Platform.

## Affected URL

https://stitchly.pwnedbox.site/index.php?action=add-to-cart

## Description

A vulnerability was identified in the e-commerce shopping cart workflow where product pricing parameters (`unit_price_cents`) are handled client-side and accepted by the server without proper server-side validation or recalculation against the authorized database records. 

An attacker can intercept the HTTP POST request when adding an item to the cart and modify the price parameter to an arbitrary low value (e.g., setting the price to `1` cent). Consequently, the item is added to the shopping cart and successfully processed through checkout at the manipulated price, leading to financial loss for the platform.

## Steps to Reproduce

1. Navigate to the target application: `https://stitchly.pwnedbox.site/`.
2. Log into the user account or create a new account.
3. Browse the shop and select any product to add to the cart while intercepting the HTTP request using a proxy tool (e.g., Burp Suite).
4. Locate the `unit_price_cents` parameter within the POST body of the `action=add-to-cart` request.
5. Modify the `unit_price_cents` value from the original price to a manipulated value (e.g., change `5800` to `1`).
6. Forward the request and navigate to the cart page to observe that the product price has been successfully altered to the manipulated value.
7. Proceed to checkout and place the order to confirm successful purchase at the fraudulent price.

## POC
<img width="773" height="883" alt="image" src="https://github.com/user-attachments/assets/2054d412-edd5-41b5-8cf0-55bb8b4043d6" />
<img width="1102" height="1054" alt="image" src="https://github.com/user-attachments/assets/d179d4b4-554f-4bca-b0ea-43a952c05989" />
<img width="1134" height="1213" alt="image" src="https://github.com/user-attachments/assets/8dd69c7b-b699-4e90-9ecf-e7569e5f5492" />
<img width="1117" height="569" alt="image" src="https://github.com/user-attachments/assets/e60b3830-309d-4e93-8bae-af0160b135be" />
<img width="883" height="536" alt="image" src="https://github.com/user-attachments/assets/cc69ddf0-dcc2-43e9-97d5-ba48398a8e5c" />


## Impact

1. Financial loss due to products being purchased significantly below their intended catalog value (e.g., purchasing items for $0.01).
2. Bypass of pricing controls and e-commerce business logic.
3. Potential exploitation at scale by malicious actors to acquire high-value inventory at negligible costs.

## Remediation

1. Implement strict server-side validation for all pricing and product information by retrieving price values directly from a secure, trusted database table using the `product_id` rather than accepting prices from client requests.
2. Remove price parameters from client-submitted data entirely during cart addition and checkout calculations.
3. Ensure that total calculations, discounts, and taxes are computed server-side within a secure session context.

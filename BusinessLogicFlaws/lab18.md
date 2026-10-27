## Title

Client-Side Price Tampering in Add-to-Cart Allows Purchasing Items at Arbitrary Prices on E-Commerce Platform.

## Affected URL

https://retailzone.pwnedbox.site/subdomains/retailzone/cart/add

## Description

A business logic vulnerability was identified in the shopping cart workflow where product pricing parameters (`price`) are handled client-side and accepted by the server without proper server-side validation against trusted database records. 

An attacker can intercept the HTTP POST request when adding a product to the cart and modify the price parameter to an arbitrary low value (e.g., changing the price to `2`). Consequently, the item is added to the user's bag and successfully processed through checkout at the manipulated price, leading to financial loss for the platform.

## Steps to Reproduce

1. Navigate to the target application: `https://retailzone.pwnedbox.site/subdomains/retailzone/register`[cite: 53].
2. Register a new user account and log into the application[cite: 53, 54].
3. Browse products and select an item to add to the bag while intercepting the HTTP request using a proxy tool (e.g., Burp Suite)[cite: 54].
4. Locate the `price` parameter within the POST body of the `/subdomains/retailzone/cart/add` request[cite: 54].
5. Modify the `price` parameter value from the original catalog price to a manipulated value (e.g., change `2999` to `2`)[cite: 54, 55].
6. Forward the request and navigate to the cart/bag page to confirm that the product price has been successfully altered to the manipulated value[cite: 56].
7. Proceed through checkout and place the order to complete the transaction at the fraudulent price[cite: 57].

## POC

<img width="1085" height="789" alt="image" src="https://github.com/user-attachments/assets/1b2bf1c9-468a-4ddd-a2d2-c009d3c8c34d" />
<img width="990" height="1098" alt="image" src="https://github.com/user-attachments/assets/d7cae442-45d3-4cd9-98e0-120820c96364" />
<img width="1082" height="1131" alt="image" src="https://github.com/user-attachments/assets/fe934873-8f1f-4db4-b5bf-c4f04efde36b" />
<img width="1121" height="623" alt="image" src="https://github.com/user-attachments/assets/7f7be0f2-a741-4477-8ed2-0d9e9e130861" />
<img width="1013" height="987" alt="image" src="https://github.com/user-attachments/assets/342a1312-d665-4f34-a685-6cc13e39795f" />


## Impact

1. Financial loss due to high-value products being purchased for a fraction of their actual cost.
2. Complete bypass of pricing integrity controls and merchandise valuation logic.
3. Potential exploitation at scale to acquire commercial goods at negligible prices.

## Remediation

1. Implement strict server-side validation for all pricing and checkout data by fetching price values directly from a secure database table using the `product_id` rather than trusting values submitted by clients.
2. Remove price parameters entirely from client-side POST requests during cart addition operations.
3. Compute all subtotals, taxes, and final order totals securely on the server side within an authenticated session context.

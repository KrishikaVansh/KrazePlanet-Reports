## Title

Negative Quantity Parameter Manipulation Allows Total Price Reduction and Arbitrary Order Cost Reduction on Marketplace Platform.

## Affected URL

https://marketplace.pwnedbox.site/index.php?action=purchases-bulk

## Description

A business logic vulnerability was identified in the order processing workflow where item quantities submitted within JSON payloads (`purchases-bulk`) accept negative integer values without proper server-side bounds validation. 

An attacker can intercept the HTTP POST request during checkout and modify an item's quantity to a negative value (e.g., changing `article_500` quantity from `1` to `-1`). Consequently, the server subtracts the item's cost from the overall order total instead of adding it, enabling users to bypass payment integrity checks and successfully place orders at a heavily reduced total cost.

## Steps to Reproduce

1. Navigate to the target application: `https://marketplace.pwnedbox.site/`.
2. Register a new user account and log in.
3. Select multiple items or content packages (e.g., 500 Words and 1000 Words) and proceed to the review order page.
4. Click confirm or trigger the checkout request while intercepting the HTTP traffic using a proxy tool (e.g., Burp Suite).
5. Locate the JSON payload sent to the `/index.php?action=purchases-bulk` endpoint containing the item quantities.
6. Modify one of the item quantities to a negative integer (e.g., change `"article_500": 1` to `"article_500": -1`).
7. Forward the request and observe that the order total is reduced by the negative item's value.
8. Complete the order confirmation to verify that the transaction is processed successfully at the manipulated total amount.

## POC

<img width="617" height="892" alt="image" src="https://github.com/user-attachments/assets/613c04c2-7537-4561-ab97-e2d199f4cc4f" />
<img width="1133" height="791" alt="image" src="https://github.com/user-attachments/assets/ab3f4c8f-b99f-4179-a485-b8cedddb0cc2" />
<img width="781" height="1080" alt="image" src="https://github.com/user-attachments/assets/91c21cbb-f181-4c5c-8f1e-d83c164ba0c2" />
<img width="993" height="644" alt="image" src="https://github.com/user-attachments/assets/d004f916-61bc-4a03-9505-d2952e02c9a8" />



## Impact

1. Financial loss for the platform as orders can be placed for a fraction of their actual value by injecting negative quantities.
2. Distortion of pricing logic, transaction totals, and wallet deduction routines.
3. Exploitation of business logic flaws to manipulate payment calculations during checkout.

## Remediation

1. Implement strict server-side input validation to ensure that item quantities only accept positive integers greater than zero (`quantity > 0`).
2. Reject any negative or zero-value quantities submitted in purchase or checkout payloads with appropriate error responses.
3. Recalculate order subtotals, discounts, and final charges securely on the server side using authenticated session states and trusted database item prices.

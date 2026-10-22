# create order (fashion-marketplace-api, Task 1)

Note: I wrote the first draft with AI help, then I traced it by hand
and ran the real code on a local test database (never the live one).

## Pseudocode

FUNCTION create order
INPUTS: the request body, with customer id, delivery address, and a
  list of items (each has a product colour id and a quantity)
OUTPUT: the new order with its items (status 201), or an error
SIDE EFFECTS: WRITE one order and all its items to the database
FAILS WHEN: the body breaks any rule in step 1; the customer does not
  exist; one or more product colours do not exist; a database call
  fails

1. Check the body against the rules:
   - customer id is a valid id
   - delivery address is not empty after trimming spaces
   - there is at least 1 item and at most 10
   - each product colour id is a valid id
   - each quantity is a whole number from 1 to 10
   - no product colour id appears twice
   IF any rule is broken
     RETURN error 422: validation error
2. CALL database to look for the customer by id (may fail)
3. IF the customer does not exist
     RETURN error 422: customer does not exist
4. Make a list of the product colour ids in the items
5. CALL database to get every product colour on that list, with its
   product name and price (may fail)
6. IF the number found is not the same as the number asked for
     RETURN error 422: one or more product colours do not exist
7. Set the total to zero
8. FOR EACH item in the request
     find its product colour from step 5
     add the product's price times the quantity to the total
     make an item record: colour id, product name, colour name,
       unit price, quantity
   END FOR
9. WRITE a new order: customer id, delivery address, the total,
   currency NGN, status pending, with all the item records
10. RETURN status 201 with the new order and its address

## Trace tables (my predictions)

Real data used: Bespoke Sneakers, price 11,100,000 (in kobo),
colours Orange and Red.

| Case | Which step gives the answer | Result |
|---|---|---|
| A: Orange x2 and Red x1 | step 10 | total 33,300,000, pending, 2 items |
| B: Orange x10 (highest allowed) | step 10 | total 111,000,000, pending, 1 item |
| C: Orange x0 (invalid) | step 1 | 422 error, nothing written |

## What the real code did

- Case A: total 33,300,000, pending, 2 items. Matched.
- Case B: total 111,000,000, pending, 1 item. Matched.
- Case C: returned 422. The order count stayed at 302 (300 from the
  seed plus my 2 real orders), so nothing was written. Matched.

## Discrepancies and corrections

no differences between trace and real code.

## Things I noticed

- Prices are stored in kobo, not naira, so 11,100,000 means 111,000
  naira. This is a unit to be careful with.
- The function never checks stock. The Purple sneaker has stock 0,
  but the function never looks at the stock number.
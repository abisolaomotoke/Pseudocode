# configureMethods (from the cors library)

## Pseudocode

FUNCTION configureMethods
INPUTS: options, an object that holds the allowed methods
OUTPUT: an object with a header name and the allowed methods as text
SIDE EFFECTS: NONE
FAILS WHEN: the options have no methods at all (the function breaks and throws an error).

1. Receive the options.
2. Get the methods from the options.
3. Check whether the methods are a list.
4. IF they are a list, join them into one text, separated by commas.
   OTHERWISE, use it as it is.
5. RETURN an object with the header name "Access-Control-Allow-Methods"
   and the methods as its value.

## Trace (my predictions)

| Input | Which step gives the answer | Answer |
|---|---|---|
| methods is the list GET, POST | step 5 | "GET,POST" |
| methods is the text "GET,POST" | step 5 | "GET,POST" |
| options has no methods at all | step 3 | fails with an error |

## What the real code printed

GET,POST, then GET,POST, then error: TypeError. It matched my predictions.

## What my first pass missed

1. FAILS WHEN said "nothing", but the function fails when methods is missing.
2. Step 4 had no "otherwise" for methods that are already text.

## Comparison with the AI explanation

The AI explanation matched my understanding. I found no mistakes in it.
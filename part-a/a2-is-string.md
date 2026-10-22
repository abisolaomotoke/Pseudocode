# isString (from the cors library)

## Pseudocode

FUNCTION isString
INPUTS: s, any JavaScript value
OUTPUT: true or false
SIDE EFFECTS: NONE
FAILS WHEN: nothing. Even null just gives false.

1. Receive a value called s.
2. Check whether the value is plain text.
3. IF the check in step 2 is true, RETURN true.
4. OTHERWISE, check whether the value is boxed text (a String object).
5. IF the check in step 4 is true, RETURN true.
6. OTHERWISE, RETURN false.

## Trace (my predictions)

| Input | Which step gives the answer | Answer |
|---|---|---|
| "hello" | step 3 | true |
| new String("hello") | step 5 | true |
| 42 | step 6 | false |
| null | step 6 | false |

## What the real code printed

true true false false. It matched my predictions. No differences.

## What my first pass missed

Step 2 was too vague. It said "the first string check" without saying what is checked.

## Comparison with the AI explanation

The AI explanation matched my understanding. I found no mistakes in it.
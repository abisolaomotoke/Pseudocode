# applyHeaders (from the cors library)

## Pseudocode

FUNCTION apply headers
INPUTS: headers, a list of header notes (each note is empty,
  or a smaller list of notes, or a note with a key and a value);
  res, the response being built for the browser
OUTPUT: nothing is returned
SIDE EFFECTS: WRITE headers onto the response; may CALL the
  Vary helper
FAILS WHEN: the list of notes is nothing at all (null), so going through it breaks and throws an error; the Vary helper or the response can also fail

1. Receive the list of notes and the response.
2. FOR EACH note in the list, from first to last
3.   IF the note is empty, skip it and move to the next note
4.   IF the note is a smaller list
       go through the smaller list note by note, using these same steps 3 to 6 on each one.
5.   OTHERWISE, IF the note's key is Vary and it has a value
       CALL the Vary helper to add the value to the response's
       Vary header (may fail)
6.   OTHERWISE, IF the note has a value
       WRITE the notes's key and value onto the response.
7. END FOR
8. RETURN nothing

## Trace (my predictions)

| Input | Which step does the key work | Result |
|---|---|---|
| One note: X-A, value 1 | step 6 | X-A: 1 |
| Nothing, then X-B with value false, then a smaller list holding X-C with value 3 | steps 3, 4 and 6 | X-C: 3 (X-B is skipped because false counts as no value) |
| Vary with value Origin, then Vary with empty value | step 5 | Vary: Origin |
| The list is nothing at all (null) | step 2 | fails with an error |

## What the real code printed

X-A 1, then X-C 3, then Vary Origin, then TypeError. It matched
my predictions.

## What my first pass missed

1. I used code words (applyHeaders, vary, setHeader) instead of plain English.
2. I did not mark the outside calls (the Vary helper and writing to the response).
3. FAILS WHEN was too vague. The function breaks when the list is nothing.

## Comparison with the AI explanation

The AI explanation matched my understanding. I found no mistakes in it.
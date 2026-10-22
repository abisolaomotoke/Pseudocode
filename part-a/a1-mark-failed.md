# markFailed (my worker, Task 2)

Note: I wrote the first draft with AI help, then I traced it by hand
and checked it against the real retry delays.

## Pseudocode

FUNCTION mark job failed
INPUTS: job (the job record: its id, tries used so far including the
  one that just failed, and tries allowed); error (what went wrong)
OUTPUT: nothing is returned
SIDE EFFECTS: changes the job's status in the database; prints one
  message
FAILS WHEN: the database write fails

1. Get the error message: IF the error is a proper error, use its
   message; OTHERWISE turn it into text
2. IF tries used is equal to or more than tries allowed
     WRITE job status dead, finish time now, last error the message
     print that the job is DEAD, with the tries and the message
     RETURN nothing
3. Work out how many times to double: tries used minus 1 (never
   less than 0)
4. Work out the base wait: backoff base, doubled that many times,
   but never more than 1 hour
5. Pick a random extra wait between 0 and the jitter limit
6. Add the extra wait to the base wait. This is the delay
7. Work out the next try time: now plus the delay (used only in
   the printed message)
8. WRITE job status failed, run time now plus the delay, last error
   the message
9. Print that the job failed, with the tries, the delay and the
   next try time
10. RETURN nothing

## Trace tables (my predictions)

| Case | Which step gives the answer | Result |
|---|---|---|
| Tries 1 of 5, proper error "boom", extra 174 | step 10 | failed, delay 2,174 |
| Tries 5 of 5 (like job 178 at the end) | step 2 | dead, no delay |
| Tries 3 of 5, plain text error "bad thing", extra 100 | step 10 | failed, delay 8,100 |

## What the real code did

Job 178 failed 5 times. The four retry delays were 2174, 4367, 8253
and 16219 ms. Each one was double the one before, plus a random extra
(174, 367, 253, 219). The fifth failure gave a DEAD line. All matched
my predictions.

## Discrepancies and corrections

no differences between trace and real code for markFailed.

## Things I noticed

- Case C (plain text error) could not be run live, because the worker
  always passes a proper error. I traced it by hand only.
# processJob (my worker, Task 2)

Note: I wrote the first draft with AI help, then I traced it by hand
and ran the real code to check it.

## Pseudocode

FUNCTION process job
INPUTS: job (one order slip from the queue: its id, its type, its
  payload with to, subject and body, how many tries it has had, and
  how many tries it is allowed)
OUTPUT: nothing is returned
SIDE EFFECTS: adds 1 then takes away 1 from the busy counter; prints
  messages; may send one email; may add a record to the sent emails
  table; changes the job's status in the database
FAILS WHEN: the job type is not send email; the payload is missing
  to, subject or body; the force fail setting is on; the email
  provider fails; a database call fails

1. Add 1 to the busy counter
2. Print how many jobs are running now
3. IF the job type is not send email
     the job has failed with "Unsupported job type"
     GO TO step 13
4. Read to, subject and body from the payload
5. IF to, subject or body is missing
     the job has failed with "Invalid payload"
     GO TO step 13
6. CALL database to look for a sent email record with this job's id
   (may fail)
7. IF a record is found
     print that this job was already sent, skipping
     WRITE job status succeeded, finish time now, last error cleared
     GO TO step 14
8. IF the force fail setting is on
     the job has failed with "force fail is enabled"
     GO TO step 13
9. IF the email delay setting is more than zero
     wait that many milliseconds
10. CALL email provider to send the email (may fail, may be slow)
11. WRITE a sent email record for this job (do nothing if one exists)
12. WRITE job status succeeded, finish time now, last error cleared
    GO TO step 14
13. FAILURE PATH: print the error, then run "mark job failed" with
    this job and the error
    (any surprise error in steps 6 to 12 also lands here)
14. ALWAYS, however we got here: take 1 away from the busy counter
    RETURN nothing

## Trace tables (my predictions)

Job A (normal): steps 1 to 6, 10, 11, 12, then 14. Email sent once,
status succeeded, counter 0.

Job B (already sent): steps 1 to 6, then step 7 jumps to step 14.
It never reaches steps 8 to 12. No email sent, status succeeded,
counter 0. The jump happens at step 7.

Job C (no body): steps 1 to 4, then step 5 jumps to step 13.
It never reaches steps 6 to 12. No email sent, counter 0.

## What the real code did

- Job A (id 176): status succeeded, 1 attempt, no error. Matched.
- Job B (id 177): log said already sent, status succeeded,
  1 attempt. Matched.
- Job C (id 178): failed 5 times, then ended as dead . Matched my
  trace up to the failure. The status was not known from my trace.

## Discrepancies and corrections

no differences, except that i first missed that step 13 prints the error before calling mark job failed.

## Things I noticed

- Job C can never succeed (its body is always missing), yet the
  worker retried it 5 times anyway.
- The email sender in my project is a pretend one (no mail server
  is set), so no real email left my computer.
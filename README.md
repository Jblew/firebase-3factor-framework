# firebase-3factor-framework
Go+JS framework for 3factor app



## Firestore job management

Constraints:

- Firestore document allows 1 write/sec

- Firestore path needs to follow a format:

  `/collection/doc/collection/doc`

A task may have the following states:

1. Waiting
2. Running
3. Done (error/success)
4. Timeout



Format:

`/[waiting|running/done/timeout]/{type}/jobs/{id}`



Runners:

- `job_executor_function<type>`
  1. Triggered by creation in `/waiting/{type}/jobs/*`
  2. Move to `/running/{type}/jobs/{id}`
  3. Execute
  4. Move to `/done/{type}/jobs/{id}`
- `timeout_returner` 
  - On schedule (every 5 minutes)
  - If timeoutOn is exceeded, moves task to `/waiting/{type}/jobs/*`



## Firestore simplified job management

- Does not handle timeouts
- Function crashes are silent
- Less costly: less writes/reads
- Faster



Format:

`/tasks_xxxcol/[waiting|done]/{type}/jobs/{id}`



Runner: `job_executor_function<type>`:

1. Triggered by creation in  `/tasks_xxxcol/waiting/{type}/jobs/*`
2. Execute
3. Move to `/tasks_xxxcol/done/{type}/jobs/{id}`



Cost:

- Writes: 2 (creation + move) ($0.039 per 100,000 documents)
- Deletes: 1 (creation + move) ()$0.013 per 100,000 documents)
- Reads: 2 (trigger+run, result read) ($0.013 per 100,000 documents)



Total: 0,117$ per 100,000 tasks


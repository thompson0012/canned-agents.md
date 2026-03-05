# Session Summary for Continuation

## Original Goal
Build a data pipeline.

## Current Focus
Fixing schema validation errors in the ingestion step.

## Decisions Made
1. **Use JSON Schema for validation** — Selected as the validation approach for the ingestion step.
2. **Batch size 1000 records** — Records will be processed in batches of 1,000.
3. **Skip invalid records with logging** — Invalid records are skipped rather than halting the pipeline; each skip is logged for review.

## Constraints
- Must process 1M records/hour.
- Max memory 4GB.

## Open Tasks
- Add retry logic.
- Implement dead letter queue.

# Command buffer

## Resource tracking

Submission-id

- descriptor sets and push constants uses current submission id to determine if the resources can be recycled.

## Planned changes

- Just in time encoding to add resource synchronization between individual commands that are simultaneously in flight.


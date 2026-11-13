# Task 2: Contracts, nested data and a star schema

**Name:** Omar Tarek
**ID:** 58-10021
**Major / lab:** MET-04

Files: `task2.ipynb` (the notebook, run top to bottom), `bookings.jsonl` (the given input, unchanged), `quarantine.jsonl` (rejected lines with their reasons), `star/` (Parquet: `dim_event.parquet`, `dim_student.parquet`, and `fact_ticket_sales/booked_month=…/`), and `requirements.txt`.

## Part D: What I did

I read 283 lines from `bookings.jsonl`. 259 bookings passed and 24 were quarantined. Of those, 21 broke the contract: 4 had a `total` that didn't equal the sum of qty × price, 4 had a malformed `student.id`, 3 were booked after the event date, 3 had a ticket `qty` below 1, 3 had an invalid ticket `type`, 2 had a missing (`null`) `event`, and 2 had an empty `tickets` list. The other 3 were a `duplicate booking_id`. The duplicate check runs after validation so that a copy only counts as a duplicate of a booking we actually kept. If we de-duplicated first and the first copy was invalid, the valid second copy would be thrown away. Here all three re-sent pairs are identical valid copies, so swapping the order gives the same counts (the notebook checks this). On a file where the first copy is bad, the counts would change. The field that drifted is `promo_code`. It first appears on 2026-10-20 (booking B0125), and every valid booking from then on has it. The contract makes it `Optional[str] = None`, so older documents without it are kept with a null `promo_code`. `total` belongs to the booking, but the fact has one row per ticket entry, so `total` is not one of its columns. If `total` were copied onto each fact row, `SUM(total)` would count a booking once per ticket line and give 42,000 EGP instead of the correct 31,285 EGP. `SUM(amount)` adds up exactly.

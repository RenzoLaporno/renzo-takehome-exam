# NOTES

## 1. Explain one fix

The double-booking bug was in `dates_overlap()`. The original check was:

```python
return start_b <= start_a <= end_b
```

This only tested whether the **new booking's start date** fell inside the existing booking's range. It missed any case where the new booking started *before* the existing one but still overlapped it. The fix uses the standard interval-overlap condition:

```python
return start_a <= end_b and start_b <= end_a
```

Two ranges overlap unless one ends entirely before the other starts, so this correctly catches every overlapping case, not just the one where the new start date lands inside the existing range.

## 2. Show the failure

Existing booking: Canon DSLR Camera, **Jan 10–15**.

Original code **wrongly allowed** a new booking for **Jan 5–12** (start before the existing booking, but clearly overlapping it Jan 10–12). The fix now correctly **blocks** it.

## 3. AI use

I used Claude Code (AI) to help read through the unfamiliar codebase, pin down the root cause of each bug, and validate/apply the fixes. I checked its output by:
- Reasoning through each fix by hand against the stated business rules (inclusive-day billing, no overlapping bookings, no booking maintenance equipment) before accepting it.
- Actually running the app and hitting the API directly (`curl`) with concrete before/after cases for each bug — e.g. re-testing the Jan 5–12 overlap, a same-day rental, and a maintenance-equipment booking attempt — to confirm the fix behaved as expected rather than trusting the diff alone.
- Reading every changed line myself rather than accepting changes blindly, and catching/correcting one mistake where a snippet ended up in the wrong function before it shipped.

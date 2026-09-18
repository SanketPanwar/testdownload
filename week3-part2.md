# Week 3 Study Packet — Part 2

## 5. FULL PROBLEM LIST

### Daily Schedule

| Day | Problem | Tag | Pattern | Target Time | Protocol Phase Budget |
|-----|---------|-----|---------|-------------|----------------------|
| Mon | Binary Search (LC 704) | [C] Easy | Index search, exact match | 25 min | 6 dry / 10 attempt / hints if needed |
| Tue | Search in Rotated Sorted Array (LC 33) | [C] Med | Rotated index search | 40 min | 10 dry / 18 attempt / hints |
| Wed | Find Minimum in Rotated Sorted Array (LC 153) | [C] Med | Rotated convergence search | 40 min | 10 dry / 18 attempt / hints |
| Thu | Search a 2D Matrix (LC 74) | [C] Med | Flattened index search | 35 min | 10 dry / 18 attempt / hints |
| Fri | Koko Eating Bananas (LC 875) | [C] Med | Answer-space search | 45 min | 10 dry / 18 attempt / hints |
| Sat Block A | Capacity To Ship Packages (LC 1011) | [C] Med | Answer-space search | 40 min | Part of deep dive |
| Sat Block A | Merge Intervals (LC 56) | [C] Med | Interval merge | 35 min | Part of deep dive |
| Sat Block A | Non-overlapping Intervals (LC 435) | [C] Med | Interval greedy (sort by end) | 40 min | Part of deep dive |
| Sat Block B | Insert Interval (LC 57) | [C] Med | Interval insert (unlabeled) | 40 min | Blind — no pattern tag |
| Sat Block B | Meeting Rooms II (LC 253) | [C] Med | Sweep-line / heap (unlabeled) | 40 min | Blind — no pattern tag |
| Sat Block B | Median of Two Sorted Arrays (LC 4) | [S] Hard | Binary search on partition (unlabeled) | 55 min | Blind — cut first if behind |

Block B includes 2 additional problems drawn from Weeks 1–2 (labels hidden). Pick any 2 you haven't revisited yet from the sliding window or two-pointer sets.

**Cut order if behind:** Drop Median of Two Sorted Arrays first. Then drop one Block B blind problem. Never cut the [C] core problems.

---

### Per-Problem Hints

Each problem has three tiers. Read only the tier you need. Stop reading the moment you have enough to proceed.

---

#### Monday — Binary Search (LC 704)

**Tier 1 — Category nudge:**
This is the simplest possible index search. Use the `lo <= hi` exact-match template (Shape B). No tricks.

**Tier 2 — Key insight:**
Compare `arr[mid]` to target. If equal, return. If less, search right half. If greater, search left half. The only thing to get right is the termination condition and the mid calculation.

**Tier 3 — Full approach:**
Initialize `lo = 0`, `hi = nums.length - 1`. Loop while `lo <= hi`. Compute `mid = lo + (hi - lo) / 2`. If `nums[mid] == target`, return `mid`. If `nums[mid] < target`, set `lo = mid + 1`. Else set `hi = mid - 1`. If the loop exits, return -1. This is a mechanical application of Template 3C from Part 1.

---

#### Tuesday — Search in Rotated Sorted Array (LC 33)

**Tier 1 — Category nudge:**
Still an index search, but the array isn't fully sorted. One half around `mid` is always sorted — figure out which one, then decide if your target lives there.

**Tier 2 — Key insight:**
At each step, check if `nums[lo] <= nums[mid]`. If so, the left half `[lo..mid]` is sorted. Check if target falls in that sorted range. If yes, search left; if no, search right. If the left half is NOT sorted, then the right half `[mid..hi]` must be sorted — apply the same logic mirrored. The key comparison for "target is in the sorted left half" is `nums[lo] <= target && target < nums[mid]` — note the strict `<` on the right side, since `mid` is already checked for equality.

**Tier 3 — Full approach:**
Use the `lo <= hi` template. First check `nums[mid] == target` for an early return. Then determine the sorted half: if `nums[lo] <= nums[mid]`, left is sorted. Within that branch, if `nums[lo] <= target < nums[mid]`, set `hi = mid - 1` (search left); else set `lo = mid + 1` (search right). In the else branch (right is sorted), if `nums[mid] < target <= nums[hi]`, set `lo = mid + 1`; else set `hi = mid - 1`. Return -1 if loop exits. Duplicates variant (LC 81): when `nums[lo] == nums[mid]`, you cannot determine the sorted half — do `lo++` and continue. This makes worst case O(n).

---

#### Wednesday — Find Minimum in Rotated Sorted Array (LC 153)

**Tier 1 — Category nudge:**
This is a convergence search (Shape A, `lo < hi`), not a hit-or-miss. You're finding the pivot point — the boundary where the array wraps around. You're not looking for a specific target value.

**Tier 2 — Key insight:**
Compare `nums[mid]` to `nums[hi]` (not to target, not to `nums[lo]`). If `nums[mid] > nums[hi]`, the minimum is to the right of mid (the rotation break is somewhere in `[mid+1, hi]`). If `nums[mid] <= nums[hi]`, the right half is sorted and mid could be the minimum, so search left including mid. This converges on the minimum.

**Tier 3 — Full approach:**
Set `lo = 0`, `hi = nums.length - 1`. Loop while `lo < hi`. Compute mid. If `nums[mid] > nums[hi]`, set `lo = mid + 1` — mid is not the minimum because something smaller exists to its right. Else set `hi = mid` — mid might be the minimum, don't exclude it. When `lo == hi`, return `nums[lo]`. Do NOT compare with `nums[lo]` — it fails on cases like `[3, 1, 2]`. The comparison must be with `nums[hi]`.

---

#### Thursday — Search a 2D Matrix (LC 74)

**Tier 1 — Category nudge:**
The matrix is a sorted array stored in 2D. Flatten the indices and run standard binary search.

**Tier 2 — Key insight:**
For a matrix with `m` rows and `n` columns, treat it as a 1D array of length `m * n`. Map linear index `mid` to 2D coordinates: row = `mid / n`, col = `mid % n`. Then run exact-match binary search on `mat[row][col]`.

**Tier 3 — Full approach:**
Set `lo = 0`, `hi = m * n - 1`. Standard `lo <= hi` loop. Compute `mid`, then `val = mat[mid / n][mid % n]`. Compare val to target. This is Template 3E from Part 1, verbatim. Watch the division: it's by `n` (number of columns), not `m`.

---

#### Friday — Koko Eating Bananas (LC 875)

**Tier 1 — Category nudge:**
This is NOT an index search. You're searching for the minimum value of `k` (eating speed) that satisfies a condition. Answer-space binary search.

**Tier 2 — Key insight:**
Define `feasible(k)`: can Koko eat all piles in `h` hours at speed `k`? For each pile of size `p`, she needs `ceil(p / k)` hours. Sum these up. If total ≤ h, feasible. The feasible region is monotone: if speed `k` works, speed `k+1` works too. Binary search for the minimum feasible `k`. Bounds: `lo = 1`, `hi = max(piles)`.

**Tier 3 — Full approach:**
Compute `maxPile` in one pass. Set `lo = 1`, `hi = maxPile`. Use the converging `lo < hi` template. For each `mid`, compute total hours as `sum of ceil(p / mid)` for each pile. Use integer ceiling: `(p + mid - 1) / mid` or `(p - 1) / mid + 1`. If total ≤ h, set `hi = mid` (mid might be the answer). Else set `lo = mid + 1` (mid is too slow). Return `lo`. This is Template 3F applied with Koko's feasible function.

---

#### Saturday Block A — Capacity To Ship Packages Within D Days (LC 1011)

**Tier 1 — Category nudge:**
Same family as Koko. You're searching for the minimum ship capacity. Answer-space binary search.

**Tier 2 — Key insight:**
`feasible(cap)`: simulate greedy loading. Go through weights left to right, accumulating onto the current day. When adding a weight would exceed `cap`, start a new day. Count total days. If days ≤ d, feasible. Bounds: `lo = max(weights)` (must hold the heaviest single package), `hi = sum(weights)` (ship everything in one day).

**Tier 3 — Full approach:**
Compute `maxW` and `sumW` in one pass. Set `lo = maxW`, `hi = sumW`. Converging template: for each `mid`, simulate: `days = 1, curLoad = 0`. For each weight: if `curLoad + w > mid`, increment days, reset `curLoad = 0`. Add `w` to `curLoad`. If `days <= d`, set `hi = mid`. Else `lo = mid + 1`. Return `lo`. Identical structure to Koko with a different feasible function.

---

#### Saturday Block A — Merge Intervals (LC 56)

**Tier 1 — Category nudge:**
Interval problem. Think about what sort order lets you merge in a single pass.

**Tier 2 — Key insight:**
Sort by start time. Then sweep left to right. If the current interval's start is ≤ the previous interval's end, they overlap — merge by extending the end to `max(prev.end, cur.end)`. If no overlap, close the previous and start a new running interval. The sort-by-start ensures you process intervals in left-to-right timeline order.

**Tier 3 — Full approach:**
Sort `intervals` by `intervals[i][0]` using `Integer.compare(a[0], b[0])`. Initialize `cur = intervals[0]`. Loop from i = 1. If `intervals[i][0] <= cur[1]`, set `cur[1] = max(cur[1], intervals[i][1])`. Else add `cur` to result list and set `cur = intervals[i]`. After the loop, add `cur` (the last interval). Return `result.toArray(new int[0][])`. Template 3G.

---

#### Saturday Block A — Non-overlapping Intervals (LC 435)

**Tier 1 — Category nudge:**
This is a greedy removal problem, not a merge problem. The sort key is different from Merge Intervals.

**Tier 2 — Key insight:**
Sort by END time. This is the activity selection problem. Greedily keep intervals that don't overlap with the last kept one: if `cur.start >= lastEnd`, keep it (update `lastEnd = cur.end`); otherwise skip it (it overlaps, so it's a removal). Count the skips — that's the minimum removals. Sorting by end ensures you always pick the interval that leaves the most room for future intervals.

**Tier 3 — Full approach:**
Sort by `intervals[i][1]` using `Integer.compare(a[1], b[1])`. Initialize `removals = 0`, `lastEnd = intervals[0][1]`. Loop from i = 1. If `intervals[i][0] >= lastEnd`, set `lastEnd = intervals[i][1]` (keep this interval). Else increment `removals` (remove this interval). Return `removals`. The key distinction from Merge Intervals: sort by end, not start, because you're selecting the maximum non-overlapping subset.

---

#### Saturday Block B — Insert Interval (LC 57) [BLIND — no pattern tag]

**Tier 1 — Category nudge:**
Interval problem. The input is already sorted. You don't need to sort.

**Tier 2 — Key insight:**
Three phases in one pass: (1) add all intervals that end before the new one starts, (2) merge all intervals that overlap with the new one by updating the new interval's bounds, (3) add all remaining intervals. No heap needed, no special data structure — just a three-phase linear scan.

**Tier 3 — Full approach:**
Initialize result list and pointer `i = 0`. Phase 1: while `i < n && intervals[i][1] < newInterval[0]`, add to result, increment i. Phase 2: while `i < n && intervals[i][0] <= newInterval[1]`, merge: `newInterval[0] = min(newInterval[0], intervals[i][0])`, `newInterval[1] = max(newInterval[1], intervals[i][1])`, increment i. Add `newInterval` to result. Phase 3: add remaining intervals. Template 3H.

---

#### Saturday Block B — Meeting Rooms II (LC 253) [BLIND — no pattern tag]

**Tier 1 — Category nudge:**
You need the maximum number of overlapping intervals at any point. Think about what changes at each event boundary.

**Tier 2 — Key insight:**
Two approaches: (a) Sort by start, use a min-heap of end times. Before adding a meeting, check if the earliest-ending room is free (heap top ≤ current start). If so, pop and reuse. Heap size at any point = rooms in use. (b) Split each interval into a +1 event at start and -1 event at end. Sort events. Sweep and track the running count. Max count = answer.

**Tier 3 — Full approach (heap):**
Sort meetings by start. Create `PriorityQueue<Integer>` (min-heap). For each meeting: if heap is not empty and `heap.peek() <= meeting[0]`, poll the heap (free room). Offer `meeting[1]` (occupy a room until this end time). Return `heap.size()`. Template 3I.

**Tier 3 — Full approach (two-array):**
Create `starts[]` and `ends[]` arrays from the meetings. Sort both independently. Use two pointers: for each start, if `start < ends[endPtr]`, a new room is needed (increment rooms); else a room was freed (increment endPtr). Return rooms.

---

#### Saturday Block B — Median of Two Sorted Arrays (LC 4) [STRETCH — cut first]

**Tier 1 — Category nudge:**
Binary search problem, but not a simple one. You're partitioning two sorted arrays simultaneously. The target is O(log(min(m, n))).

**Tier 2 — Key insight:**
Binary search on the shorter array. For each candidate partition position `i` in the short array, compute the corresponding partition `j` in the long array such that `i + j = (m + n + 1) / 2` (left half size). Check four boundary values: `maxLeftA, minRightA, maxLeftB, minRightB`. The partition is correct when `maxLeftA <= minRightB && maxLeftB <= minRightA`.

**Tier 3 — Full approach:**
Ensure `nums1` is the shorter array (swap if needed). Set `lo = 0`, `hi = m` (length of shorter). Loop `lo <= hi`. `i = (lo + hi) / 2`, `j = (m + n + 1) / 2 - i`. Handle edge cases with `Integer.MIN_VALUE` and `Integer.MAX_VALUE` for out-of-bounds partitions. If `maxLeftA > minRightB`, move left: `hi = i - 1`. If `maxLeftB > minRightA`, move right: `lo = i + 1`. Else partition is correct: compute median from the four boundary values. Odd total: `max(maxLeftA, maxLeftB)`. Even total: average of `max(maxLeftA, maxLeftB)` and `min(minRightA, minRightB)`. This is the hardest problem of the week and uses a unique binary search variant — if it doesn't click by Saturday evening, defer to Week 16 (hard problem review).

---

## 6. RECOGNITION CUES + DECISION TREE

### Decision Tree for Pattern Selection

```
START: Read the problem statement.
  │
  ├─ Are you given a set of intervals/ranges/meetings/time spans?
  │     │
  │     YES ──► INTERVAL PROBLEM
  │     │   ├─ "Merge overlapping," "union," "total covered length"
  │     │   │     → Sort by START. Sweep and merge.
  │     │   ├─ "Max non-overlapping," "minimum removals," "activity selection"
  │     │   │     → Sort by END. Greedy keep-or-skip.
  │     │   ├─ "Max concurrent," "min rooms/resources," "overlap count"
  │     │   │     → Sweep-line event counting OR min-heap on end times.
  │     │   └─ "Insert into sorted intervals"
  │     │         → Three-phase linear scan (before / merge / after).
  │     │
  │     NO ──▼
  │
  ├─ Is the input sorted (or rotated sorted)?
  │     │
  │     YES ──► INDEX BINARY SEARCH
  │     │   ├─ "Find exact value" → lo <= hi, return mid on hit.
  │     │   ├─ "Find first/last occurrence" → lowerBound / upperBound.
  │     │   ├─ "Sorted but rotated" → Identify sorted half, decide which to search.
  │     │   └─ "Sorted 2D matrix" → Flatten to 1D, standard binary search.
  │     │
  │     NO ──▼
  │
  ├─ Are you asked to minimise/maximise a value, AND can you write a
  │  feasible(x) check in O(n), AND is the feasible region monotone?
  │     │
  │     YES ──► ANSWER-SPACE BINARY SEARCH
  │     │   Key test: "If x works, does x+1 also work?" (for min)
  │     │   or "If x works, does x-1 also work?" (for max).
  │     │   If yes → define lo, hi as value bounds, binary search.
  │     │
  │     NO ──▼
  │
  ├─ Does the problem involve a contiguous subarray with a sum/count constraint?
  │     → Likely SLIDING WINDOW or PREFIX SUM (Weeks 1–2).
  │       ├─ Fixed-size window → fixed sliding window.
  │       ├─ Variable-size, all values non-negative → shrink-while-invalid.
  │       ├─ Subarray sum equals k, with negatives → prefix sum + HashMap.
  │       └─ "At most k distinct" or count → atMost(k) - atMost(k-1).
  │
  ├─ Does the problem involve pairs with a constraint (sum, difference)?
  │     → Likely TWO POINTERS on sorted input or HASHING (Week 1).
  │
  └─ None of the above → re-read the problem. Check constraints.
      n ≤ 10⁵ with an O(n log n) expectation often hides a sort + binary search.
      "Minimum maximum" or "maximum minimum" is almost always answer-space search.
```

### Trigger Phrases for Answer-Space Search

These phrases in a problem statement are strong signals:

- "Minimize the maximum ___"
- "Maximize the minimum ___"
- "Minimum speed/capacity/size such that ___"
- "Can you do it in at most D days/trips/groups?"
- "Allocate ___ to minimize/maximize ___"
- Constraint: `1 ≤ answer ≤ 10⁹` (giant value range = they want you to binary search it, not linear scan)

### The Monotonicity Test

Before committing to answer-space search, verify monotonicity in one sentence:

- Koko: "If she can finish at speed k, she can finish at speed k+1." ✓ Monotone.
- Ship capacity: "If capacity c ships in D days, capacity c+1 also ships in ≤ D days." ✓ Monotone.
- Counter-example: "Find the value of x that maximizes f(x) where f is a parabola." ✗ Not monotone — f goes up then down. Binary search won't work.

### Sort-Key Decision for Intervals

| Question Being Asked | Sort By | Why |
|---|---|---|
| Merge/union overlapping intervals | **Start** | Process left-to-right; extend end on overlap |
| Count merged groups / total coverage | **Start** | Same as merge |
| Insert a new interval into sorted list | Already sorted by start | Three-phase scan |
| Maximum non-overlapping (activity selection) | **End** | Earliest finish = greediest safe choice |
| Minimum removals to make non-overlapping | **End** | Equivalent to n minus max non-overlapping |
| Max concurrent / min rooms | **Start** (with heap on ends) | OR split into events, sort events by time |

---

### Classification Drill — 6 Problem Snippets

Read each snippet. Classify it as one of: (A) Index binary search, (B) Answer-space binary search, (C) Interval merge/sweep, (D) Sliding window, (E) Prefix sum, (F) Two pointers. Time yourself — target 30 seconds each.

**Snippet 1:**
"Given an array of meeting time intervals `[[s1,e1],[s2,e2],...]`, determine the minimum number of conference rooms required."

**Snippet 2:**
"You have `n` packages with weights `w[i]` that must be shipped in order. Find the minimum capacity of the ship so that all packages are shipped within `D` days."

**Snippet 3:**
"Given a sorted array of integers, find the first and last position of a given target value. Return `[-1, -1]` if not found."

**Snippet 4:**
"Given a string, find the length of the longest substring with at most `k` distinct characters."

**Snippet 5:**
"Given a sorted array that has been rotated at some pivot, find the minimum element."

**Snippet 6:**
"Given an array of integers and a target sum, find the number of contiguous subarrays whose sum equals the target. The array may contain negative numbers."

---

**Drill Answers** (don't read until you've classified all 6):

1. **(C) Interval sweep** — "meeting time intervals" + "minimum rooms" = sweep-line or min-heap.
2. **(B) Answer-space binary search** — "minimum capacity such that" + monotone predicate + large value range.
3. **(A) Index binary search** — sorted array + "first and last position" = lowerBound + upperBound.
4. **(D) Sliding window** — "longest substring with at most k" = variable window with shrink-while-invalid.
5. **(A) Index binary search** — sorted + rotated + "find minimum" = rotated convergence search.
6. **(E) Prefix sum** — "number of subarrays with sum equals target" + negatives present = prefix sum + HashMap.

---

## 7. REVISION NOTES / FLASHCARDS

Format: Q on front, A on back. Designed for Anki import (one card per pair).

---

**Card 1**
Q: What is the difference between lowerBound and upperBound in one sentence?
A: lowerBound returns the first index where `arr[i] >= target`; upperBound returns the first index where `arr[i] > target`. The only code difference is `<` vs `<=` in the comparison.

**Card 2**
Q: In the `lo < hi` convergence template, what is `hi` initialized to?
A: `hi = arr.length` (one past the end), NOT `arr.length - 1`. This allows the answer to be "past everything" when no element satisfies the condition.

**Card 3**
Q: In the `lo < hi` template, why is `hi = mid` (not `mid - 1`) in the else branch?
A: Because `mid` might be the answer. We can't exclude it. The invariant is "the answer is in [lo, hi]" and mid satisfies the condition, so it must stay in the window.

**Card 4**
Q: Why use `mid = lo + (hi - lo) / 2` instead of `(lo + hi) / 2`?
A: To prevent integer overflow. `lo + hi` can exceed `Integer.MAX_VALUE` when both are large. `(hi - lo)` is always safe because `hi >= lo`.

**Card 5**
Q: What is the time complexity of answer-space binary search, and why?
A: `O(n · log(range))`, where range is the answer space size (hi - lo). The log is over the value range, not the array length. Each of the `log(range)` iterations calls feasible() which costs O(n).

**Card 6**
Q: What are the two trigger signals for answer-space binary search?
A: (1) The problem asks to minimise/maximise a value. (2) There exists a monotone feasible(x) function — once true, it stays true for all larger (or smaller) x.

**Card 7**
Q: Why does Non-overlapping Intervals sort by end, not by start?
A: It's the activity selection problem. Sorting by end and greedily picking the earliest-finishing interval maximises the number of non-overlapping intervals. Sorting by start can pick a long interval that blocks many short ones.

**Card 8**
Q: Why does Merge Intervals sort by start?
A: You process intervals left to right. Sorting by start ensures that when you encounter a new interval, all previously seen intervals have earlier or equal starts. You only need to check if the current start falls within the running interval's range.

**Card 9**
Q: What is the concrete problem with `Arrays.binarySearch()` for duplicates?
A: The Java spec says it returns *an* index matching the target, with no guarantee *which* one. You cannot use it for "find first occurrence" or "find last occurrence" — it may return any of the duplicates. Hand-roll lowerBound/upperBound instead.

**Card 10**
Q: In rotated sorted array search, what do you compare `nums[mid]` to?
A: Compare `nums[lo]` to `nums[mid]` to determine which half is sorted. If `nums[lo] <= nums[mid]`, the left half is sorted. Then check if target is in that sorted range. (For "Find Minimum," compare `nums[mid]` to `nums[hi]` instead.)

**Card 11**
Q: How do you convert a linear index `mid` to 2D coordinates in an m×n matrix?
A: `row = mid / n`, `col = mid % n`. Divide and mod by the number of COLUMNS, not rows.

**Card 12**
Q: Name all six TreeMap lookup methods and whether they include the key itself.
A: Inclusive: `floorKey(k)` (greatest ≤ k), `ceilingKey(k)` (smallest ≥ k). Exclusive: `lowerKey(k)` (greatest < k), `higherKey(k)` (smallest > k). Endpoints: `firstEntry()` (smallest), `lastEntry()` (largest). Mnemonic: floor/ceiling touch the key, lower/higher don't.

**Card 13**
Q: What is the ceiling division formula in Java without using `Math.ceil` or doubles?
A: `(a + b - 1) / b` for positive a and b. Alternative that avoids overflow if a is large: `(a - 1) / b + 1` (valid when a > 0).

**Card 14**
Q: Why is the Comparator `(a, b) -> a[0] - b[0]` unsafe for sorting intervals?
A: Integer overflow. If `a[0]` is near `Integer.MAX_VALUE` and `b[0]` is negative (or vice versa), the subtraction overflows. Use `Integer.compare(a[0], b[0])` instead — always safe.

**Card 15**
Q: In Meeting Rooms II (min-heap approach), what does `heap.peek() <= meeting.start` mean?
A: The earliest-ending meeting in the heap finishes at or before this meeting starts, so that room is free and can be reused. Poll it and push the new meeting's end time.

---

## 8. END-OF-WEEK SELF-TEST

Complete all 5 questions in one sitting. No notes, no IDE autocomplete. Paper or blank text file only. Time limit: 45 minutes total.

---

### Question 1 — Recognition (5 minutes)

Read the following problem statement and classify it. State: (a) which pattern it uses, (b) what you sort by (if anything), (c) time complexity.

> "A company has `n` employees. Each employee has a work shift `[start_i, end_i]`. Find the maximum number of employees working at the same time."

---

### Question 2 — Recognition (5 minutes)

Read the following problem statement and classify it. State: (a) which pattern, (b) what `lo` and `hi` should be, (c) what the feasible function checks.

> "A farmer has `n` cows and `m` stalls at positions `pos[i]` along a line. Place all `n` cows into distinct stalls such that the minimum distance between any two cows is maximised. Return that maximum minimum distance."

---

### Question 3 — Template Recall: lowerBound (8 minutes)

Write the complete `lowerBound(int[] arr, int target)` method from memory. Include the return type, correct initialisation, loop condition, mid calculation, comparison, and branch assignments. Then hand-trace it on `arr = [1, 3, 3, 5, 7]`, `target = 3`, showing lo/hi/mid at each iteration.

---

### Question 4 — Template Recall: Answer-Space Skeleton (8 minutes)

Write the complete answer-space binary search skeleton for a minimisation problem. Include the `feasible()` helper signature, the correct lo/hi loop, and the return value. Then write the concrete `feasible()` for this problem: "Given an array of positive integers `arr` and an integer `maxOps`, find the minimum possible maximum value after splitting any element into at most `maxOps` additional pieces." (LC 1760 — Minimum Limit of Balls in a Bag). Define lo, hi, and the feasible check.

---

### Question 5 — Applied Problem (19 minutes)

Solve the following from scratch, using this week's patterns. Write clean Java. State the pattern, the time complexity, and the space complexity before coding.

> "Given an array of `n` positive integers representing the heights of vertical lines at positions `0..n-1`, and a number `k` representing the number of horizontal planks available, find the minimum width of a plank such that all `k` planks can be used to partition the lines into groups (each plank covers a contiguous segment of lines, and a plank of width `w` can cover lines whose maximum height minus minimum height is ≤ w). Return the minimum plank width."

*Hint: this is NOT a sliding window problem despite "contiguous segment."*

---

### Scoring Rubric

| Question | Points | Criteria |
|----------|--------|----------|
| Q1 | 10 | Correct pattern (interval sweep/event counting): 5. Correct sort (by time/events): 3. Correct complexity O(n log n): 2. |
| Q2 | 10 | Correct pattern (answer-space binary search): 4. Correct lo = 0 or 1 (min possible distance): 2. Correct hi = max gap between stalls: 2. Correct feasible description (greedy placement, check if n cows fit with min gap ≥ x): 2. |
| Q3 | 20 | `hi = arr.length` (not length-1): 4. `lo < hi` (not <=): 4. `mid = lo + (hi-lo)/2`: 3. `arr[mid] < target → lo = mid+1`: 3. `else → hi = mid`: 3. `return lo`: 3. Correct trace (3 iterations, returns 1): required for full marks, no partial if trace is wrong. **Any off-by-one error in the code = 0/20.** |
| Q4 | 20 | Correct skeleton structure with `lo < hi` convergence: 8. Correct feasible() for balls-in-bag (for each ball of size `s`, pieces needed = ceil(s/maxVal) - 1, sum ≤ maxOps): 8. Correct lo = 1, hi = max(arr): 4. **Any off-by-one in the skeleton = 0/20.** |
| Q5 | 40 | Correct identification as answer-space BS: 10. Correct feasible (greedy scan: traverse lines, track min/max in current segment, if max-min > w start new segment, count segments ≤ k): 15. Correct lo/hi bounds (lo = 0, hi = max(arr) - min(arr)): 5. Clean, compiling Java: 5. Correct complexity statement O(n log(range)): 5. |
| **Total** | **100** | |

### Pass/Fail Threshold

| Score | Verdict |
|-------|---------|
| **80+** | Proceed to Week 4. |
| **60–79** | Repeat the self-test after 2 days of targeted review on the sections you dropped points. Do NOT repeat the full week. |
| **Below 60** | Repeat the week. Focus specifically on the template you got wrong. A single off-by-one on Q3 or Q4 means the template is not yet automatic — re-drill it 3 times from a blank file before retesting. |

**Hard rule on Q3 and Q4:** These are worth 20 points each. A *single* off-by-one (wrong hi initialisation, `<=` instead of `<`, `mid-1` instead of `mid`) is scored as **0**, not partial credit. In an interview, an off-by-one in binary search is a failing signal because the interviewer will assume you'll produce the same errors in all search code you write. Bounds discipline is binary — it's correct or it's wrong.

---

*End of Part 2. End of Week 3 Study Packet.*

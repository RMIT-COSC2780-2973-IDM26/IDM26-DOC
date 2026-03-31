# Flight Network Assignment FAQ

- [Flight Network Assignment FAQ](#flight-network-assignment-faq)
  - [Updates to the spec](#updates-to-the-spec)
  - [Should the output/results of queries be listed in the order as in the spec?](#should-the-outputresults-of-queries-be-listed-in-the-order-as-in-the-spec)
  - [Can I use built-in predicate `P/n`?](#can-i-use-built-in-predicate-pn)
  - [How can I compute \& carry "so-far" results?](#how-can-i-compute--carry-so-far-results)
  - [Can there be decimals in duration and price of flight legs?](#can-there-be-decimals-in-duration-and-price-of-flight-legs)
  - [Exercise 3: should the tools also check for proper flight paths?](#exercise-3-should-the-tools-also-check-for-proper-flight-paths)

## Updates to the spec

- See [this post](https://edstem.org/au/courses/29085/discussion/3217233) for:
  - Fix price and duration values in the example of Exercise 4.
  - Clarify query 3 what "just" means.
  - Add a note about redundant answers and ordering of answers in queries.

## Should the output/results of queries be listed in the order as in the spec?

No! When we have answers from Prolog, we really do not care their order, they are logically true answers. Order doesn't matter. 😉

## Can I use built-in predicate `P/n`?

This project assignment can be solved neatly using plain basic Prolog built-in predicates. As the Language Restriction section in the specs states you are not to use advanced predicates or libraries. These include all-solution predicates like `findall/3` for example or the constraint libraries: none are needed anyways.

If in doubt always ask in the forum. Some other predicates that you shall not use are:

- `table/1`, `order_by/1`, `sort/2`

Some predicates that you can use as they are still basic are:

- `nth0/3`, `nth1/3`, `reverse/2`, `min/2`, `max/2`, `last/2`.
- All the type checking predicates: https://www.swi-prolog.org/pldoc/man?section=typetest
- Any of the standard order of terms (e.g., `@</2`): https://www.swi-prolog.org/pldoc/man?section=standardorder


## How can I compute & carry "so-far" results?

A usual strategy for computing something is to carry intermediate results until the end, and once there, just equate the final result with the result so far.

For example, this is how I can sum a list of numbers:

```prolog
sumar(L, R) :- sumar(L, 0, R).

sumar([], SumSoFar, SumSoFar).
sumar([X|L], SumSoFar, Result) :-
    SumSoFar2 is  SumSoFar + X,
    sumar(L, SumSoFar2, Result).
```

Check a run:

```prolog
?- sumar([1,2,3,4], R).
R = 10.
```

## Can there be decimals in duration and price of flight legs?

No. Duration (in minutes) and price (in $) are always _integers_.

## Exercise 3: should the tools also check for proper flight paths?

No. You can assume that the `Path` given is always a proper legal flight path, that is, it passes/succeeds on `flight_path/1`. 😃

This is for all predicates in the exercise, namely, `path_price/2`, `path_duration/2`, and `path_airlines/2`.

If one were to use it somewhere, one would first check that the path is a proper correct flight path, and then use these tools.

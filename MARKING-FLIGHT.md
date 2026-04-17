# Marking Guide for Flkight Network Reasoner Project - IDM'26

This document explains the automarking framework and system used for the Flight Network Reasoner assignment project.

In order to understand the output from the automarker, there are two distinct components:

1. the different test set cases we graded with; and
2. the dimensions each tests is evaluated/checked against.

The automarker will produce a YAML file describing the outcome of each single test in each test set. See below for an example of a part of such YAML file.

- [Marking Guide for Flkight Network Reasoner Project - IDM'26](#marking-guide-for-flkight-network-reasoner-project---idm26)
  - [Consult error-free](#consult-error-free)
  - [Test suites cases](#test-suites-cases)
  - [Grading dimensions](#grading-dimensions)
    - [Completeness](#completeness)
    - [Redundancy](#redundancy)
    - [Soundness](#soundness)
  - [Overall score/marks](#overall-scoremarks)
  - [Report](#report)
    - [Forbidden predicates](#forbidden-predicates)
    - [Predicate dependencies and best modes](#predicate-dependencies-and-best-modes)
    - [Test 8 for `best_trip/5 `: Inadequate implementation against requirement](#test-8-for-best_trip5--inadequate-implementation-against-requirement)

## Consult error-free

Of course the very first and most basic requirement to get any marks is that your codebase compiles error free, so that the automarker can load it up as a library in full:

```
* Your code **must consult and run _error-free_ on [SWI-Prolog](https://www.swi-prolog.org/)**. Staff will not debug/fix any code.
```

This is super easy to check, you just consult your solution file and it should report NO error whatsoever:

```shell
$ swipl
Welcome to SWI-Prolog (threaded, 64 bits, version 8.4.2)
SWI-Prolog comes with ABSOLUTELY NO WARRANTY. This is free software.
Please run ?- license. for legal details.

For online help and background, visit https://www.swi-prolog.org
For built-in help, use ?- help(Topic). or ?- apropos(Word).

?- ['flight_ssytem.pl'].
true.
```

An alternative way is to consult your file when starting SWI-Prolog:

```shell
$ swipl flight_system.pl
Welcome to SWI-Prolog (threaded, 64 bits, version 8.4.2)
SWI-Prolog comes with ABSOLUTELY NO WARRANTY. This is free software.
Please run ?- license. for legal details.

For online help and background, visit https://www.swi-prolog.org
For built-in help, use ?- help(Topic). or ?- apropos(Word).

?-
```

The above cases report no error, so file `flight_system.pl` have been consulted (i.e., loaded) successfully.

In contrast, here is an example of a file that is _not_ error free:

```shell
$ swipl
Welcome to SWI-Prolog (threaded, 64 bits, version 8.4.2)
SWI-Prolog comes with ABSOLUTELY NO WARRANTY. This is free software.
Please run ?- license. for legal details.

For online help and background, visit https://www.swi-prolog.org
For built-in help, use ?- help(Topic). or ?- apropos(Word).

?- ['flight_system.pl'].
ERROR: ./submissions/bad/flight_system.pl:146:35: Syntax error: Unexpected end of file
true.
```

or simply:

```shell
$ swipl flight_system.pl
ERROR: ./submissions/bad/flight_system.pl:146:35: Syntax error: Unexpected end of file
Welcome to SWI-Prolog (threaded, 64 bits, version 8.4.2)
SWI-Prolog comes with ABSOLUTELY NO WARRANTY. This is free software.
Please run ?- license. for legal details.

For online help and background, visit https://www.swi-prolog.org
For built-in help, use ?- help(Topic). or ?- apropos(Word).

?-
```

One can see in the above cases that the file has syntax problems in line 164 and hence it yields ERROR. A file like this will not be marked and will attract zero marks, as per spec. When this happens the report will signal it with the following feedback:

```plaintext
- 'Solution file consulted with error (this should never happen): syntax_error(end_of_file)'
```

(Note that interactive SWI-Prolog and unit testing framework will still load the file _partially_ and reporting the error, so as to help in the debugging. But ultimately the codebase has to be error-free.)

## Test suites cases

The automarker runs a set of question **_test suites_**, each worth certain number of marks. Overall, the test suites sum up to 100 marks (plus some bonus marks).

In general a test suite checks a given predicate. However, a predicate marking can be split across several test suites. For example, each routing predicate has two test suites: the sum of their marks amount to the weight of the corresponding exercise.

Then, each test suite includes a set of **_unit test cases_**, each testing a particular feature or quality. Unit tests attract points or deductions, and are usually numbered. All the points available across all test cases in a suite contribute to the test suite's marks. So,  in order to collect all the marks of the test suite, one has to collect all points in it. So, if a test set is worth 5 marks and it has 10  test cases of 1 point each, collecting 5 points amounts to 2.5 marks for the test set.

Finally, some test cases are deemed as **BONUS**. These are tests that go beyond what we asked or expect. There is 6% bonus points for grab. The bonus are only for this assignment, so the max mark in the assignment itself is 100.

## Grading dimensions

For Prolog, each standard single test case takes the form of a query. The expected answer is the expected results of the query. The query results can be either a boolean value (if no variables are involved in the query), or one or more sets of bindings for the free variables in the query (which we will refer to as a solution). For all queries, we check three properties: _completeness_, _redundancy_, and _soundness_.

### Completeness

This is where we check whether the implementation is complete, that is, it yields all the right solutions. For a boolean result, this is a binary `true`/`false` check.

For a solution (answers with bindings), the calculation is more complex. In general, a query may have a _set_ of intended solutions (each with different bindings), so the automarker checks how many of them are returned as answers, and award partial marks proportional to the number of solutions found.

In some cases, though, we are more interested in some variable bindings than others. In this case, we sometimes award partial marks to solutions which are not fully correct (they are not exactly one of the expected answers), but are close to one correct answer. For example, if you have the expected answer: `A = apple, B  = banana, X = 3`, and the student answer: `A = apple, B = banana, X = 4`, this may be eligible for _partial_ marks (even though, technically, the answer is incorrect).

An implementation which is (fully) _complete_ is given 90% of the total marks for the question. These marks are indicated in the marking report by lines such as:\

```plaintext
Test N: [P] - Completness: X/Y
```

where `N` is the ID of the test case, `P` is the number of points awarded to this answer, `X` is the number of solutions generated (that were able to be at least partially matched to the expected set), and `Y` is the number of solutions expected  (always 1 for a boolean output).

### Redundancy

We next check for answers redundancy. This is important, as inefficient Prolog programs can often generate large numbers of identical solutions, which is not particularly useful.

We simply identify if there are any duplicates in the solution set generated. If not, the answer gets the remaining 10% of the total marks for the question. These marks are indicated in the marking report by lines such as:

```plaintext
Test N: [P] - No redundant answers found! :-)
```

or

```plaintext
Test N: [P] - Redundant answers found...
```

Where `N` is the ID of the test case, and `P` is the number of points awarded to this answer for redundancy.

To be eligible for these marks, the query must have non-zero completeness; i.e. At least one correct solution must have been generated. This is to avoid trivial programs from receiving these marks just because they always return one answer (e.g. `true`).

### Soundness

Finally, we check if the answer is _sound_, that is, whether it never yields _incorrect_ answers. Unsoundness is indeed a serious problem: producing wrong solutions is generally worse than failing to produce some expected solutions. As a result, if there are any unexpected solutions found (i.e., those that cannot even be partially matched against an expected solution), the score is reduced by 70% of the marks that you have obtained in the completeness and redundancy stages.

Soundness checks are not applied to boolean solutions (as it is the same as the completeness check).

These marks are indicated in the marking report by lines such as:

```plaintext
Test N: [-P] - Incorrect answers found... (e.g., S)
```

where `N` is the ID of the test case, `P` is the number of points subtracted from this answer for soundness failure, and `S` is one example of a solution that was unexpected.

## Overall score/marks

Ultimately, the points received by the student for a standard test case is given by the following formula:

$$points = (0.9\cdot t\cdot p_c + 0.1\cdot m \cdot b_r) \cdot 0.3\cdot b_s$$

where:

* $t$ is the total points allocated to that test case,
* $p_c$ is the ratio of correct solutions generated (including partial matches) -- 1 is all expected solutions generated,
* $b_r$ is a binary redundancy flag set to $1$ if _no redundant_ solutions were found, and
* $b_s$ is a binary soundness flag set to $1$ if any _unsound_ solutions were found (which means -70% discount if wrong answers are returned).

This is indicated in the marking report by lines such as:

```plaintext
Test N: [P/T] - Final points collected
```
where `N` is the ID of the test case, `P` is the number of points received by the submission, and `T` is the total points available.

## Report

The automarker will produce a YAML file with the summary of the results for each test set. The report will follow the framework above.

An example of the section that scores full marks is as follows:

```yaml
  path_reverse:
    desc: checking path_reverse/2
    points: 13.0
    points_available: 13
    bonus_available: 0
    marks: 2.0
    marks_available: 2
    feedback:
    - 'Test 1: Checks empty path for path_reverse/2 '
    - 'Test 1: [0.90] - Completeness: 1/1'
    - 'Test 1: [0.10] - No redundant answers found! :-)'
    - 'Test 1: [1.00/1.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 2: Checks 1 leg path for path_reverse/2 '
    - 'Test 2: [0.90] - Completeness: 1/1'
    - 'Test 2: [0.10] - No redundant answers found! :-)'
    - 'Test 2: [1.00/1.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 3: Checks 2 leg path with different airlines for path_reverse/2 '
    - 'Test 3: [1.80] - Completeness: 1/1'
    - 'Test 3: [0.20] - No redundant answers found! :-)'
    - 'Test 3: [2.00/2.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 4: Checks 2 leg path with same airlines path_reverse/2 '
    - 'Test 4: [1.80] - Completeness: 1/1'
    - 'Test 4: [0.20] - No redundant answers found! :-)'
    - 'Test 4: [2.00/2.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 5: Checks 3 leg path with 3 airline changes (but only 2 airlines total) for path_reverse/2 '
    - 'Test 5: [2.70] - Completeness: 1/1'
    - 'Test 5: [0.30] - No redundant answers found! :-)'
    - 'Test 5: [3.00/3.00] - Final points collected for test case - Time: 0.13s'
    - 'Test 6: Checks long path for path_reverse/2 '
    - 'Test 6: [3.60] - Completeness: 1/1'
    - 'Test 6: [0.40] - No redundant answers found! :-)'
    - 'Test 6: [4.00/4.00] - Final points collected for test case - Time: 0.12s'
```

In turn, an example where the report signals issues is as follows:

```yaml
   best_trip:
    desc: best_trip/5
    points: 2
    points_available: 37
    bonus_available: 0
    marks: 1.62
    marks_available: 30
    feedback:
    - 'Test 1: Checks invalid query for best_trip/5 (no path)'
    - 'Test 1: [2.00] - Correct answer! :-)'
    - 'Test 1: [2.00/2.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 2: Checks basic query for best_trip/5'
    - 'Test 2: [0.00] - Completeness: 0/1 (e.g., [{''X'': [[''madrid'', ''iberia'', ''toronto'']], ''P'': 875, ''D'': 525, ''N'': 1}])'
    - 'Test 2: [0.00] - No correct answers given, not eligible for redundancy marks'
    - 'Test 2: [-0.00] - Incorrect answers found (1)... (e.g., [{''X'': [[''madrid'', ''iberia'', ''toronto'']], ''P'': 800, ''D'': 480, ''N'': 0}]) :-( )'
    - 'Test 2: [0.00/4.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 3: Checks basic query for best_trip/5'
    - 'Test 3: [0.00] - Completeness: 0/1 (e.g., [{''X'': [[''london'', ''air_canada'', ''toronto'']], ''P'': 575, ''D'': 440, ''N'': 1}])'
    - 'Test 3: [0.00] - No correct answers given, not eligible for redundancy marks'
    - 'Test 3: [-0.00] - Incorrect answers found (1)... (e.g., [{''X'': [[''london'', ''air_canada'', ''toronto'']], ''P'': 500, ''D'': 360, ''N'': 0}]) :-( )'
    - 'Test 3: [0.00/4.00] - Final points collected for test case - Time: 0.13s'
    - 'Test 4: Checks query for best_trip/5 with length 2 path'
    - 'Test 4: [0.00] - Completeness: 0/1 (e.g., [{''X'': [[''barcelona'', ''iberia'', ''valencia''], [''valencia'', ''iberia'', ''malaga'']], ''P'': 230, ''D'': 245, ''N'': 1}])'
    - 'Test 4: [0.00] - No correct answers given, not eligible for redundancy marks'
    - 'Test 4: [-0.00] - Incorrect answers found (1)... (e.g., [{''X'': [[''barcelona'', ''air_canada'', ''madrid''], [''madrid'', ''iberia'', ''malaga'']], ''P'': 150, ''D'': 120, ''N'': 0}]) :-( )'
    - 'Test 4: [0.00/9.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 5: Checks query for best_trip/5 with length 2 path'
    - 'Test 5: [0.00] - Completeness: 0/1 (e.g., [{''X'': [[''barcelona'', ''air_canada'', ''madrid''], [''madrid'', ''iberia'', ''malaga'']], ''P'': 265, ''D'': 195, ''N'': 2}])'
    - 'Test 5: [0.00] - No correct answers given, not eligible for redundancy marks'
    - 'Test 5: [-0.00] - Incorrect answers found (1)... (e.g., [{''X'': [[''barcelona'', ''air_canada'', ''madrid''], [''madrid'', ''iberia'', ''malaga'']], ''P'': 150, ''D'': 120, ''N'': 0}]) :-( )'
    - 'Test 5: [0.00/9.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 6: Checks query for best_trip/5 with length 3 path'
    - 'Test 6: [0.00] - Completeness: 0/1 (e.g., [{''X'': [[''valencia'', ''iberia'', ''madrid''], [''madrid'', ''iberia'', ''toronto''], [''toronto'', ''air_canada'', ''london'']], ''P'': 1430, ''D'': 1015, ''N'': 2}])'
    - 'Test 6: [0.00] - No correct answers given, not eligible for redundancy marks'
    - 'Test 6: [-0.00] - Incorrect answers found (1)... (e.g., [{''X'': [[''valencia'', ''iberia'', ''madrid''], [''madrid'', ''iberia'', ''toronto''], [''toronto'', ''air_canada'', ''london'']], ''P'': 1340, ''D'': 890, ''N'': 0}]) :-( )'
    - 'Test 6: [0.00/9.00] - Final points collected for test case - Time: 0.13s'
    - 'Test 7: Checks best_trip/5 returns all fastest routes, not just one'
    - 'Test 7: [0.00] - Correct answer! :-)'
    - 'Test 7: [0%/-20%] - Final points collected for test case - Time: 0.12s (BONUS)'
    - 'Test 8: Checks best_trip/5 implemented iteratively as required by spec'
    - 'Test 8: [0.00] - Correct answer! :-)'
    - 'Test 8: [0%/-90%] - Final points collected for test case - Time: 0.12s'
```

In this fragment, there are 8 single tests. Each has been checked for completeness, redundancy, and soundness. It shows issues on all three dimensions.

> ![NOTE]
> See how the report provides descriptions of each test and also the time taken to execute your solution! 😉

### Forbidden predicates

When a test is found to use a forbidden predicate, as per the "Language restrictions and guidelines ⚡" section in the project spec, the test will attract no points and be reported as follows:

```yaml
    - 'Test 2: [0.00] - Test failed due to exception: A forbidden predicate has been
      used: {''findall''}'
```

In this case, the test used the `findall/3` predicate which was not allowed.

It is the MINIMUM expectation to adhere to these rules. No partial marks will be awarded when these rules are broken or ignored.

### Predicate dependencies and best modes

Some predicates (e.g., `best_trip/5`) will be marked twice: your solution "as is", and with our perfect solutions for the pre-requisites (e.g., bounded_route/7). **We take the case that scores most points!** 😉

This deals with solutions that are in themselves correct, but use incorrectly implemented previous predicates (which were already marked before).

So, we would run each test suite in two modes:

1. using your previous predicates, even if buggy and not fully correct.
2. using our perfect implementation of previous predicates;

We would then take the one that achieves more points. You will see that in the report as follows:

```plaintext
   best_mode: You scored the most points when we ran your code with your implementations
   for prior questions, so all results for this question are using your implementations.
```

### Test 8 for `best_trip/5 `: Inadequate implementation against requirement

Test 8 in `best_trip/5` makes sure the implementation of `best_trip/5` is iterative, as required by the spec. If it is not, then it will attract a 90% deduction on the points for that test case, which can be significant. 

This is an example of an implementation that passes all tests but fails this one, signalling that it didn't respect the requirement of being iterative:

```yaml
  best_trip:
    desc: best_trip/5
    points: 3.6999999999999993
    points_available: 37
    bonus_available: 0
    marks: 3.0
    marks_available: 30
    feedback:
    - 'Test 1: Checks invalid query for best_trip/5 (no path)'
    - 'Test 1: [2.00] - Correct answer! :-)'
    - 'Test 1: [2.00/2.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 2: Checks basic query for best_trip/5'
    - 'Test 2: [3.60] - Completeness: 1/1'
    - 'Test 2: [0.40] - No redundant answers found! :-)'
    - 'Test 2: [4.00/4.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 3: Checks basic query for best_trip/5'
    - 'Test 3: [3.60] - Completeness: 1/1'
    - 'Test 3: [0.40] - No redundant answers found! :-)'
    - 'Test 3: [4.00/4.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 4: Checks query for best_trip/5 with length 2 path'
    - 'Test 4: [8.10] - Completeness: 1/1'
    - 'Test 4: [0.90] - No redundant answers found! :-)'
    - 'Test 4: [9.00/9.00] - Final points collected for test case - Time: 0.13s'
    - 'Test 5: Checks query for best_trip/5 with length 2 path'
    - 'Test 5: [8.10] - Completeness: 1/1'
    - 'Test 5: [0.90] - No redundant answers found! :-)'
    - 'Test 5: [9.00/9.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 6: Checks query for best_trip/5 with length 3 path'
    - 'Test 6: [8.10] - Completeness: 1/1'
    - 'Test 6: [0.90] - No redundant answers found! :-)'
    - 'Test 6: [9.00/9.00] - Final points collected for test case - Time: 0.12s'
    - 'Test 7: Checks best_trip/5 returns all fastest routes, not just one'
    - 'Test 7: [0.00] - Correct answer! :-)'
    - 'Test 7: [0%/-20%] - Final points collected for test case - Time: 0.13s (BONUS)'
    - 'Test 8: Checks best_trip/5 implemented iteratively as required by spec'
    - 'Test 8: [-0.90] - Incorrect answer (wrong bool answer)'
    - 'Test 8: [-90%/-90%] - Final points collected for test case - Time: 0.12s'
```
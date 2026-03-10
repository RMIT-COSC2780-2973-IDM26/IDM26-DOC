# Prolog FAQ and Guidelines

- [Prolog FAQ and Guidelines](#prolog-faq-and-guidelines)
  - [General Prolog Information ℹ️](#general-prolog-information-ℹ️)
    - [Development environment](#development-environment)
    - [Loading files](#loading-files)
    - [Singleton variables](#singleton-variables)
    - [Use of `;/2`](#use-of-2)
    - [Strings as atoms](#strings-as-atoms)
    - [Efficiency](#efficiency)
    - [Duplicate redundant answers](#duplicate-redundant-answers)
    - [Use of NAF `\+` on non-grounded goals](#use-of-naf--on-non-grounded-goals)
  - [Other Prolog information and issues 👣](#other-prolog-information-and-issues-)
    - [Anonymous variables](#anonymous-variables)
    - [Difference between `=/2` and `==/2`](#difference-between-2-and-2)
    - [Evaluating and comparing arithmetic expressions: the `is/2` construct](#evaluating-and-comparing-arithmetic-expressions-the-is2-construct)
    - [Variables in all-solution predicates?](#variables-in-all-solution-predicates)
  - [Style matters 💇](#style-matters-)
    - [Indentation](#indentation)
    - [Repeated code](#repeated-code)
    - [Documentation](#documentation)
  - [Unit Testing in SWI-Prolog ✅](#unit-testing-in-swi-prolog-)
    - [I get "`ERROR: -g run_tests(distance): append_args/3: Unknown procedure: '$messages':to_list/2`"](#i-get-error--g-run_testsdistance-append_args3-unknown-procedure-messagesto_list2)
    - [I get warning on "Test succeeded with choicepoint", why?](#i-get-warning-on-test-succeeded-with-choicepoint-why)
  - [Issues 😕](#issues-)
    - [Why do I get  "`Warning: Clauses of a/1 are not together in the source-file`" warning?](#why-do-i-get--warning-clauses-of-a1-are-not-together-in-the-source-file-warning)

## General Prolog Information ℹ️

Simpson's DB: use grandpa (not granpa)
Make sure SWI is in your PATH so you can call it from terminal
In Linux, install the test package (swi-prolog-test)
Put the same predicate all together, don't scattered the around interleaved: SWI will complain.
Always use an IDE to do your development (e.g., VSCODE with Prolog plugin) - do not upload or edit in GitHub
sits_right(X, Y): Y sits to the left of X (X to the right of Y)

### Development environment

We highly and strongly recommend using a proper IDE for developing Prolog code. The best we know so far today is [Visual Studio Code (VSCode)](https://code.visualstudio.com/) with extension [NEW-VSC-Prolog](https://github.com/AmauryRabouan/new-vsc-prolog). You will get syntax highlighting, snippets and completion suggestions, hover information, grammar linter (fundamental!), and many many other features for proper development.

### Loading files

Unless specified otherwise, one should not load a database in the same file where the reasoning rules are located. For example, if we have a database of Uni courses in file `courses.pl` and a Prolog reasoning system to reason about program plans named `uni_programs.pl`, then the latter should NOT load `courses.pl` itself, as it should work with any course database possible.

Instead, to test it one can consult both as follows:

```prolog
-? ['courses.pl'].
-? ['uni_programs.pl'].
```

Alternatively, one can design a single program `main.pl` with:

```prolog
:- ['courses.pl'].
:- ['uni_programs.pl'].
```

and just consult `main.pl`.

### Singleton variables

To get more marks and more robust code, we strongly suggest to **avoid singleton variables!** A singleton variable is a variable that only occurs once in the arguments or the body of a predicate and is thus useless. For example in the following two definitions:

```prolog
calculate(X, Y, Z) :- Z is X + 2.
```

Here `Y` is a singleton variable, and should be prefixed by an underscore `_Y` or replaced by an anonymous variable `_`.

Singleton variables make it harder to read the code and can signal a bug, like in the following example:

```prolog
inc(FirstNr, Result) :- Result is Firstnr + 1.
```

Here, both `FirstNr`and `Firstnr` will be reported as singleton variables! This is very useful information because it often helps you finding typos, a common source of bugs in Prolog, as shown above (`Firstnr` should have been `FirstNr`).

SWI-Prolog will point out any singleton variables you have:

```shell
Warning: flight_system.pl:181:
Warning:    Singleton variables: [Y]
```

Take those warnings seriously as soon as they come up, do not just ignore them: check if it signals a bug and resole the issue so the warning is gone.

**NOTE:** one can change tell SWI-Prolog to _not_ check for singletons via [style_check/1](https://www.swi-prolog.org/pldoc/doc_for?object=style_check/1). However this is NOT recommended for the reasons stated above and should NOT be done in your project assignments unless given explicit written permission from teaching staff.

### Use of `;/2`

It is generally OK to use `;/2` as part of a simple _if-then-else_ construct when the whole logic is simple, for example to set a variable:

```prolog
(<condition> -> Speed = 0 ; Speed = MaxSpeed)
```

This is fine as it only sets `Speed` and has a very simple logic, no complex code is either branch of the "if" construct.

However, code like this one:

```prolog
p(X,Y,Z) :-
 <code A> ; <code B>.
 ```

should instead be written as two separate clauses:

```prolog
p(X,Y,Z) :- <code A>.
p(X, Y,Z):- <code B>.
```

### Strings as atoms

In Prolog, any string starting with capital letter is considered a variable. So, to represent a string a name like "Alan Turing" you have two options:

1. You explicitly single quote it, e.g., `'Alan Turing'` to indicate the whole name is an atom.
2. You use snake case notation, e.g., `alan_turing`.

Note that SWI Prolog does include a type String, but is used mostly for I/O operations and where the text does not denote an identifier.

In most cases, you would want to represent the names as _atoms_.

See [this explaination](https://www.swi-prolog.org/pldoc/man?section=string) for more information.

### Efficiency

Unless specifically stressed, you don't have to go out of your way for optimization purposes. However, the code must aim to be reasonably efficient. You should not be traversing the lists unnecessarily, for example. Basically, without doing crazy tricks, your code should aim to be efficient.

### Duplicate redundant answers

In general, you should eliminate duplicates whenever you can. Sometimes, however, you may not be able to eliminate all duplicates, and that's OK. In each query, you should make a decision as to whether or not it is possible to eliminate duplicates.  Sometimes there are different causes of duplicates, and you can deal with one, but not the other. It is part of the task to figure it out when it is doable and when it is not.

### Use of NAF `\+` on non-grounded goals

Using NAF on open goals (i.e., goals containing variables) can result in unexpected and undesired outcomes.  Consider:

```prolog
% Defines what animals are known
animal(X) :- animal_class(X, _).

% Facts defining animals and their classes
animal_class(dog, mammal).
animal_class(cat, mammal).
animal_class(horse, mammal).
animal_class(eagle, bird).
animal_class(penguin, bird).
animal_class(sparrow, bird).
animal_class(cobra, reptile).
animal_class(iguana, reptile).
animal_class(frog, amphibian).
animal_class(salmon, fish).
animal_class(shark, fish).
animal_class(butterfly, insect).
animal_class(ant, insect).
```

We can get all the birds via query:

```prolog
?- animal_class(X, bird).
X = eagle ;
X = penguin ;
X = sparrow.
```

All good, what about getting all animals that are not birds? Well we just use negation of the above query, right?

```prolog
?- \+ animal_class(X, bird).
false.
```

We didn't get all the animals that are NOT birds: the query just failed! The reason is that `\+ G` translate to "it is not possible to prove G", but as we have seen above, the query `animal_class(X, bird)` can be proven in three ways!

A more extreme case is this:

```prolog
?- \+ X=1.
false.
```

Again, goal query `X = 1`can indeed be proven trivially by unifying `X` to `1`!

As a general rule, you should always make sure the goal inside a negation-as-failure (NAF) `\+` will be fully grounded at evaluation time.

So, to get all the animals known that are NOT birds, we instead write:

```prolog
?- animal(X), \+ animal_class(X, bird).
X = dog ;
X = cat ;
X = horse ;
X = cobra ;
X = iguana ;
X = frog ;
X = salmon ;
X = shark ;
X = butterfly ;
X = ant.
```

By the time Prolog evalutes the sub-goal `\+ animal_class(X, bird)`, the variable `X` has been bounded to a specific known animal:

```shell
?- animal(X).

X = dog ;
X = cat ;
X = horse ;
X = eagle ;
X = penguin ;
X = sparrow ;
X = cobra ;
X = iguana .
```

If we fix the `X` to a possible value, say `dog`, then `\+ animal_class(dog, bird)` will indeed succeed, because one cannot prove that `animal_class(dog, bird)`!

So, every time you use `\+`, be very careful and think: could it ever be the case that the goal is not fully instantiated?

> [!WARNING]
> Constructs like `\=` are shorthands for NAF queries, in this case, `\+ =`. So `A \= B` is a shorthand for `\+ A = B`. Thus all the explanation above applies to such constructs! 😉

> [!NOTE]
> ❓ What would the meaning of `\+ animal_class(X, bird)` be then? Since this will succeed only if the goal cannot be proven at all, it would be more like "there is no animal that is a bird"! So, it behaves like a universally quantified query, which is often not what one is after...

## Other Prolog information and issues 👣

### Anonymous variables

Anonymous variables can be used when we we do not care about its value, as long as it exists one. Anonymous variables are represented with an underscore (`_`).

For example, consider this program:

```prolog
q(1).
q(2).
q(3).

p(1, a).
p(1, b).
p(8, c).
p(9, d).
```

If we want to check if `q()` is true, but we don't care for which values:

```prolog
?- q(_).
true ;
true ;
true.
```

The query has succeeded three times, as there are three possible values that make the query true, but the actual bindings are not shown, as we have used an anonymous variable.

Each occurrence of `_` is treated as a different, independent variable, as the following example shows:

```prolog
?- q(_), p(_, Y).
Y = a ;
Y = b ;
Y = c ;
Y = d ;
Y = a ;
Y = b ;
Y = c ;
Y = d ;
Y = a ;
Y = b ;
Y = c ;
Y = d.
```

If we wanted to list all the `q`-elements that have a related `p` element, then we need to use:

```prolog
?- q(X), p(X, Y).
X = 1,
Y = a ;
X = 2,
Y = b ;
false.
```

If we are merely interested in that there exists such `X` joining the two predicates, but not in its value:

```prolog
?- q(_X), p(_X, Y).
_X = 1,
Y = a ;
_X = 2,
Y = b ;
false.

?- set_prolog_flag(toplevel_print_anon, false).
true.

?- q(_X), p(_X, Y).
Y = a ;
Y = b ;
false.
```

The flag [`toplevel_print_anon`](https://www.swi-prolog.org/pldoc/man?section=flags) states whether anonymous variables `_X` are to be printed or not.

### Difference between `=/2` and `==/2`

**NOTE:** consider the differences between [`=/2`](https://www.swi-prolog.org/pldoc/doc_for?object=(%3D)/2) (equal as per unification) and [`==/2`](https://www.swi-prolog.org/pldoc/doc_for?object=(%3D%3D)/2) (syntactically equal), and their corresponding dual versions:

```prolog
?- X = 1.
X = 1.

?- X == 1.
false.

?- X \= 1.
false.

?- X \== 1.
true.

?- A == B.
false.

?- A = B.
A = B.
```

Note also that `\=` and `\==` are equivalent to `\+ =` and `\+ ==` (i.e., it is not possible to prove ....).

### Evaluating and comparing arithmetic expressions: the `is/2` construct

Be mindful how Prolog evaluates arithmetic expressions as it is different from languages like Python or Java. An arithmetic expression is just a (compound) term and, unlike Python for example, Prolog will not evaluate them when part of an argument in a predicate. For example:

```prolog
?- number(2).
true.

?- number(3-1).
false.

?- functor(3-1,X,Y).
X =  (-),
Y = 2.
```

Here `3-1` is NOT `2`, it is the term `3-1` (with functor `-` and arity 2!).

So, to do evaluation of an expression, you should use the [`is/2`](https://www.swi-prolog.org/pldoc/doc_for?object=(is)/2) built-in construct. So, instead of writing things like `distance(X, Y, N-1)`, you should instead write `distance(X, Y, N2), N2 is N-1`. It is very important that at the time of execution of an `is/2` goal, the expression is ground and there are no variables:

```prolog
?- N is (3+2)*8.
N = 40.

?- X = 8, N is (3+2)*X.
X = 8,
N = 40.

?- N is (3+2)*X.
ERROR: Arguments are not sufficiently instantiated
ERROR: In:
ERROR:   [10] _6050 is (3+2)*_6058
ERROR:    [9] toplevel_call('<garbage_collected>') at /usr/lib/swi-prolog/boot/toplevel.pl:1158
?-
```

See how in the last goal, the `X` is still a variable, so Prolog is not able to evaluate a non-ground  expression. _Can you understand why?_

Finally, note that the same above issues apply when using comparison relations, like `>` or `<`: the two sides cannot be uninstantiated and they will not be evaluated automatically:

```prolog
?- 2 < X.
ERROR: Arguments are not sufficiently instantiated
ERROR: In:
ERROR:   [10] 2 <_2596
ERROR:    [9] toplevel_call(user:user: ...) at /usr/lib/swi-prolog/boot/toplevel.pl:1158

?- 2+3 > 0.
true.

?- X is 2+3, X > 0.
X = 5.
```

The only evaluating comparison operator that is provided is `=:=/2` which is True if two expressions evaluate to the same number. This could be simpler than using `is/2` to evaluate first and then `=/2` to compare later:

```prolog
?- 2+5 =:= 3+4.
true.

?- 2+5 = 3+4.
false.

?- X1 is 2+5, X2 is 3+4, X1 = X2.
X1 = X2, X2 = 7.
```

### Variables in all-solution predicates?

Free variables behave differently across `findall/3`, `bagof/3`, and `setof/3`. This is important to understand when one is trying to extract all the variables in a term.

Consider this query:

```prolog
?- findall(X, member(X, [A, B, C]), L), A = 1.
A = 1,
L = [_, _, _].
```

You can see that the list `L` constructed by `findall/3` does NOT contain the actual variable `A` or otherwise `L = [1, _, _]`.

This is because, as explained in [`findall/3` documentation](https://www.swi-prolog.org/pldoc/doc_for?object=findall/3), `findall/3` is like `bagof/3` with _all the free variables mentioned in the goal are assumed to be existentially quantified_ (that is, we don't care about them as long as there is a binding for them making the goal true):

```prolog
?- bagof(X, A^B^C^member(X, [A, B, C]), L), A=1.
A = 1,
L = [_, _, _].
```

The point is that the variable `A`  in `A=1` is NOT the same as the one appearing in the goal of `bagof/3`, as that one is under the existential quantification scope so it is local to `bagof/3`.

Contrast this with `bagfof/3` without quantifying on the free variables:

```prolog
?- bagof(X, member(X, [A, B, C]), L), A=1.
A = 1,
L = [1, B, C].
```

Then we we get all the results collected in `L` due to full backtracking of the goal in question, namely, `member(X, [A, B, C]))`.

Finally, consider this more complex run:

```prolog
?- bagof(X, B^(member(X, [A, B, C]), member(B, [hello, bye])), L), A=1, B=2.
B = 2,
A = 1,
L = [1, 1, hello, bye, C, C]
```

The reason why `hello` and `by` appear in `L`, is because `X` is matched with `B` in the first `member/2`, and then `B` to each of the two terms. Since the second `member/2` succeeds twice, we get also copies of variables `A` and `C` in `L`, as `bagof/3` collects all instances of the template (`X` here) for each success of the goal.

## Style matters 💇

Pay attention to good style, including:

- Simpler, shorter code is generally best.
- Be _consistent_ your naming conventions, you can use [camel case](https://en.wikipedia.org/wiki/Camel_case) or [snake case](https://en.wikipedia.org/wiki/Snake_case) (the one I tend to use), but just be consistent.
- Try to use unification variables when possible; for example, write

    ```prolog
    swap([A,B], [B,A]).
    multiply(N, 1, N).
    ```

    rather than

    ```prolog
    swap(L1, L2) :- L1 = [A,B], L2 = [B,A].
    multiply(N, M, X) : M = 1, X = N.
    ```

- Pick intuitive names for your predicates and arguments.
  - Good naming convention for a helper predicate used by a predicate `pred` is `pred_aux` (`aux` stands for"auxiliary"). This makes it easier to understand what it is used for.
- Put all the clauses of the same predicate together, avoid interleaving predicates predicates; see [this post](#why-do-i-get--warning-clauses-of-a1-are-not-together-in-the-source-file-warning) below.

### Indentation

In Prolog, indentation is not strictly required for the correct execution of the code, as Prolog relies on the position of terms rather than indentation to determine the structure of the program.

However, proper indentation is crucial for code readability and maintaining a clear structure. As always, your code should be easy to read.

A common practice is to put every subgoal on a new line. For example, instead of:

```prolog
% Define a complex rule using multiple predicates.
complex_rule(A, B, C, D, E, F) :- predicate1(A), predicate2(B), predicate3(C), predicate4(D), predicate5(E), predicate6(F).
```

you should instead write:

```prolog
% Define a complex rule using multiple predicates.
complex_rule(A, B, C, D, E, F) :-
    predicate1(A),
    predicate2(B),
    predicate3(C),
    predicate4(D),
    predicate5(E),
    predicate6(F).
```

### Repeated code

You should avoid repeating code and having separate rules that look almost identical. Instead, try to  have one piece of "main logic," and the different cases should be handles in a helper predicate, for example.

### Documentation

Each predicate you write should have a concise and clear documentation also indicating its mode of usage. You can find plenty of good documentation examples in the SWI-Prolog manual and even help system (e.g., try `help(append)`).

For **documentation style** similar to JavaDoc, refer to [SWI-Prolog Source Documentation](https://www.swi-prolog.org/pldoc/doc_for?object=section(%27packages/pldoc.html%27)).

## Unit Testing in SWI-Prolog ✅

Many workshop exercises include testing cases that can be used against your solution. To do this, we rely on SWI-Prolog Unit Testing framework library [plunit](https://www.swi-prolog.org/pldoc/doc_for?object=section(%27packages/plunit.html%27)). Make sure you install that module when you install and setup your SWI-Prolog; in Linux Ubuntu, you need to install package `swi-prolog-test`.

To run a set the test cases, consult your solution file (in this case, `family.pl`) and the file defining the test cases (in our case `test/test_02_03.pl`), and then run the built-in predicate goal `run_tests/0`, which will run al test cases:

```shell
$ swipl
Welcome to SWI-Prolog (threaded, 64 bits, version 9.2.2)
SWI-Prolog comes with ABSOLUTELY NO WARRANTY. This is free software.
Please run ?- license. for legal details.

For online help and background, visit https://www.swi-prolog.org
For built-in help, use ?- help(Topic). or ?- apropos(Word).

?- ['family.pl'].
true.

?- ['test/test_02_03.pl'].
true.

?- run_tests.
% PL-Unit: father ...... done
% PL-Unit: mother ...... done
% PL-Unit: grandmother ...... done
% PL-Unit: grandfather ...... done
% PL-Unit: sister ..... done
% PL-Unit: aunt ..... done
% PL-Unit: uncle ... done
% PL-Unit: ancestor ..... done
% All 42 tests passed
true.
```

You can run also a specific set of test cases. For example, to run just the `sister` test set:

```prolog
?- run_tests(sister).
% PL-Unit: sister ..... done
% All 5 tests passed
true.
```

To avoid manually consulting the relevant files, we can start SWI-Prolog with files to be consulted at start:

```shell
$ swipl family.pl test/test_02_03.pl
Welcome to SWI-Prolog (threaded, 64 bits, version 9.2.2)
SWI-Prolog comes with ABSOLUTELY NO WARRANTY. This is free software.
Please run ?- license. for legal details.

For online help and background, visit https://www.swi-prolog.org
For built-in help, use ?- help(Topic). or ?- apropos(Word).

?- run_tests.
[42/42] ancestor:ancestor_4 ................................................... passed (0.000 sec)
% PL-Unit: father ...... done
% PL-Unit: mother ...... done
% PL-Unit: grandmother ...... done
% PL-Unit: grandfather ...... done
% PL-Unit: sister ..... done
% PL-Unit: aunt ..... done
% PL-Unit: uncle ... done
% PL-Unit: ancestor ..... done
% All 42 tests passed
```

Even more, we can also directly state the top-level goal to run when calling SWI-Prolog:

```shell
$ swipl -g run_tests -t halt family.pl test/test_02_03.pl
% PL-Unit: father ..... passed 0.000 sec
% PL-Unit: mother ..... passed 0.000 sec
% PL-Unit: grandmother ..... passed 0.000 sec
% PL-Unit: grandfather ..... passed 0.000 sec
% PL-Unit: sister .... passed 0.000 sec
% PL-Unit: aunt .... passed 0.000 sec
% PL-Unit: uncle .. passed 0.000 sec
% PL-Unit: ancestor .... passed 0.000 sec
% All 34 tests passed
```

The `-t halt` will make SWI-Prolog exit after the goal `run_tests` has completed.

You can, and probably should, add your own test cases. Study the
[plunit](https://www.swi-prolog.org/pldoc/doc_for?object=section(%27packages/plunit.html%27)) on how to define new test cases and check the test cases already provided.

### I get "`ERROR: -g run_tests(distance): append_args/3: Unknown procedure: '$messages':to_list/2`"

For example:

```shell
$ swipl -g run_tests -t halt cs_dinner.pl test_03.pl
% PL-Unit: distance
% No tests to run
ERROR: -g run_tests: append_args/3: Unknown procedure: '$messages':to_list/2
```

This often happens when there is no definition for the predicate being tested, in this case there is no definition for predicate `distance/3` and hence no test can be run.

### I get warning on "Test succeeded with choicepoint", why?

Sometimes you will see a test passing but with a warning as follows:

```shell
  % PL-Unit: grandfather ..
  Warning:
      [[ EXCEPTION while printing message url('/home/runner/work/workshop-2-ssardina/workshop-2-ssardina/family/test/test_02_03.pl':96)
         with arguments []:
         raised: type_error(text,url('/home/runner/work/workshop-2-ssardina/workshop-2-ssardina/family/test/test_02_03.pl':96))
      ]]
  :
   PL-Unit: Test grandfather_1: Test succeeded with choicepoint
  .... done
  % All 7 tests passed
```

This warning is just saying that the answers returned were OK and as expected, but that the implementation left nonetheless some "choice" open to backtrack, even though they won't lead to more answers.

The best fix is to make the implementation more deterministic, but sometimes it is not possible to achieve that.

A very comprehensive explanation, discussion, and ways to address it can be read in the stackoverflow discussion [here](https://stackoverflow.com/questions/40711908/what-is-a-test-succeeded-with-choicepoint-warning-in-pl-unit-and-how-do-i-fix), highly recommended if you want to grasp a fine understanding of what is happening here... :-)

## Issues 😕

### Why do I get  "`Warning: Clauses of a/1 are not together in the source-file`" warning?

SWI-Prolog will, by default, assume that all clauses of a predicates are all together, without other predicate definitions in the middle. This makes the code more readable as everything for a predicate is "there".

If you want to disable the warning for a particular predicate, use [:- discontiguous/1](https://www.swi-prolog.org/pldoc/man?predicate=discontiguous/1) directive.


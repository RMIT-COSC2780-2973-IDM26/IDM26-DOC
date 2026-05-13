# Examination Timetabling Allocation Project - FAQ

- [Examination Timetabling Allocation Project - FAQ](#examination-timetabling-allocation-project---faq)
  - [General](#general)
  - [Validator 🤞](#validator-)
  - [Modeling](#modeling)
    - [Optional data and default values](#optional-data-and-default-values)
    - [Are timeslots ids consecutives?](#are-timeslots-ids-consecutives)
    - [Can I change identifiers of exams, rooms, timeslots to lowercase?](#can-i-change-identifiers-of-exams-rooms-timeslots-to-lowercase)
  - [Solver Application](#solver-application)
    - [My Clingo is taking minutes to ground! Why?](#my-clingo-is-taking-minutes-to-ground-why)
    - [What if the output folder does not exist?](#what-if-the-output-folder-does-not-exist)
    - [Can my application output more than the calendar?](#can-my-application-output-more-than-the-calendar)
    - [Respect time-limit!](#respect-time-limit)
    - [Respect Clingo constants given!](#respect-clingo-constants-given)
    - [Need a particular Python package beyond standard ones?](#need-a-particular-python-package-beyond-standard-ones)
  - [Clarifications on constraints](#clarifications-on-constraints)
    - [What are F1 and F2?](#what-are-f1-and-f2)
    - [For cost FC, what is `N`?](#for-cost-fc-what-is-n)
    - [Timeslot coincidence](#timeslot-coincidence)
    - [Enforced timeslots and rooms](#enforced-timeslots-and-rooms)
    - [Combinable Rooms](#combinable-rooms)
    - [Global capacity: no. of students or exams?](#global-capacity-no-of-students-or-exams)
    - [Impossible exams: what should we do with them?](#impossible-exams-what-should-we-do-with-them)
    - [Timeslot earliness and spread constraints](#timeslot-earliness-and-spread-constraints)

## General

- Input format may have redundancies, e.g., size of an exam. it is NOT a bug, it is a natural feature of real datasets. Dealing well with this is part of understanding and modeling!

## Validator 🤞

A separate post was done in [#71](https://edstem.org/au/courses/29085/discussion/3307388) about this.

You can use our validator. Note that there is an initial template code provided under `validator/` in the source code. But this is ONLY if you want to aim for HD level in the end after you have completed Levels 1-3.

Do not work on the validator until you have met all other requirements!

>[!Caution]
> When using the validator, make sure you tell it if you have downcased the identifiers, via option `--downcase`. Check [this question](#can-i-change-identifiers-of-exams-rooms-timeslots-to-lowercase) below.

## Modeling

### Optional data and default values

Default values should be used on optional data that may not show up in the YAML file of some instances. Defaults are common sense, but they are listed in the documentation within the benchmark folder.

### Are timeslots ids consecutives?

Yes! You can assume that tiemslots ids are integers and they represent the order of the timeslots, so that timeslot n+1 is the next timeslot after timeslot n.

### Can I change identifiers of exams, rooms, timeslots to lowercase?

There are many identifiers in the ETTF, for example, exam IDs, room IDs, student IDs, etc. Different dataset use different IDs, such as numbers (e.g., `23`) or strings (e.g., `STU-8801` or `stu-8801`).

When using Logic Programming, strings starting with capital letters are interpreted as variables, while strings starting with lowercase letters are interpreted as atoms. Therefore, it is important to be consistent with the capitalisation of identifiers, and to ensure that they are correctly interpreted by the validator and the solver.

💁‍♂️ We recommend **dealing with identifiers in their original form**, and quote them as needed to make sure they are dealt as atoms (e.g., `"STU-8801"`) and not as variables.

However, if you want to downcase the identifiers, you can do so, but make sure to be consistent and uniform in doing so:

- Either you downcase _all_ identifiers (e.g., `stu-8801`) or none (e.g., `"STU-8801"`), but do not mix the two styles.
- When downcasing, the whole identifier should be downcased, including the prefix (e.g., `"STU-8801"` should be downcased to `stu-8801`), and the same applies to all identifiers (e.g., exams, rooms, etc).
- Remember that integer 23 is not the same as atom '23'.

If you downcase the identifiers, you should tell so to the validator via option `--downcase`, so that it can correctly interpret the identifiers as atoms.

## Solver Application

### My Clingo is taking minutes to ground! Why?

As we know, there are two steps in the solving process: grounding and solving. Grounding is the process of generating a propositional representation of the problem, while solving is the process of finding a solution to the grounded problem (via search).

If your Clingo is taking minutes to ground, it is likely that your encoding is not efficient, and it is generating a very large grounded program. There are many reasons why this can happen, and finding them is indeed one of the aims of the project, as it is part of understanding and modeling the problem well. So, do not get frustrated, but rather try to understand why this is happening and how to fix it.

If your encoding is taking a long time to ground, try to find the subset of your encoding that is causing the problem, and try to optimize it. Do baby-sets and add rules incrementally. Remember that rules do interact with each other, so the problem may not be in a single rule, but in the interaction between rules. 😉

Also, you can look at the grounding itself by running Clingo with option `--text`:

```shell
$ clingo --text <program.lp>
```

This will show you the grounded program, and you can diagonose how different type of rules are grounded (e.g., choice rules, constraints, aggreations, etc), so that you better understand what to avoid doing!

As a reference, my solution for the core NOTT instance problem (with no room allocation, 32 slots, with F1 and F2 cost) takes around 3 seconds to ground and 15 seconds to solve, producing 50+ models and solving it optimally with zero cost:

```shell
$ exam-allocate benchmarks/nott/instance.yaml --const tmax=32 --const prox1_cost=3 --const prox2_cost=1 --const allocate_rooms=0 --output-dir results-new/test -t 2
...
...
...
2026-05-13T11:26:40 [exam-allocate] INFO | Waiting indefinitely for next solution
2026-05-13T11:26:40 [exam-allocate] INFO |     Model 55, Time: 0.392s/13.154s/16.013s, Cost: [11775], Optimal: False
2026-05-13T11:26:40 [exam-allocate] INFO | Waiting indefinitely for next solution
2026-05-13T11:26:42 [exam-allocate] INFO |     Model 56, Time: 2.297s/15.464s/18.323s, Cost: [0], Optimal: False
2026-05-13T11:26:42 [exam-allocate] INFO | Waiting indefinitely for next solution
2026-05-13T11:26:42 [exam-allocate] INFO | Last solution just proven Clingo optimal, hurray!
2026-05-13T11:26:42 [exam-allocate] INFO |     Model 56, Time: 0.037s/15.514s/18.373s, Cost: [0], Optimal: True
2026-05-13T11:26:42 [exam-allocate] INFO | LAST MODEL: Model 56, Time: 0.037s/15.514s/18.373s, Cost: [0], Optimal: True
2026-05-13T11:26:42 [exam-allocate] INFO | Grounding: 2.858 seconds - 0.05 minutes
2026-05-13T11:26:42 [exam-allocate] INFO | Solving: 15.514 seconds - 0.26 minutes - 0.00 hours
2026-05-13T11:26:42 [exam-allocate] INFO | Total: 18.373 seconds - 0.31 minutes - 0.01 hours
2026-05-13T11:26:42 [exam-allocate] INFO | CLINGO Time States:
 {
  "total": 20.35759997367859,
  "cpu": 32.142519,
  "solve": 11.990308046340942,
  "unsat": 0.04989194869995117,
  "sat": 0.5059781074523926
}
```

Note the story changes as I reduce `tmax` to lower values, as the problem becomes harder to solve, (though the grounding itself is, as expected, not much affected). Also note that I used 2 threads; using just one will generally increase the solving time dramatically.

When including room allocation (`--const allocate_rooms=1`), grounding goes up to 6+ seconds (as we now have room objects to consider) and solving time 70+ seconds, but still solved optimally.

I hope this gives you a reference point to understand if your grounding time is reasonable or not.  🙏 Observe that you are not meant to match my numbers, different encodings and different runs will produce different times, but if your grounding it takin 3 minutes, then you might have an issue! 😉

### What if the output folder does not exist?

The option `--output-dir` tells your solver where to leave your outputs, including the (best) calendar file `calendar.tsz`.

If the folder doesn't exist, your application should create it.

### Can my application output more than the calendar?

YES! And most probably you will want to output more than just the calendar, e.g., some logs, or some intermediate results, or even some other files with different formats. This is totally fine, as long as you also output the calendar in the right format and filename, and leave it in the right folder.

Think about debugging and auditing your runs.

### Respect time-limit!

Your system MUST be able to leave your last best calendar found even if your whole application is terminated, say with a kill command in Linux. There is a  `--time-limit` option built in Clingo and I provided some initial code to propagate it to parameter `timeout` 🕐, so you may want to design your system to track time so far and terminate _gracefully_ before the deadline.

If your system does not output the calendar before being terminated, it will be taken as that it just cannot solve the problem by the autograder.

> [!Caution]
> Leaving the best calendar found so far, and in the right format and filename, is a **minimal requirement**. Teaching staff will not engage in fixing and elaborating your system.

### Respect Clingo constants given!

The template provided defines several constants that will be used to configure the type of timetabling requested. Your application MUST respect them 100%. 💯

So if the system is called with option `-allocate_rooms=-1` then we are asking to allocate the rooms, and if option `-prox1_cost 7` is used, your solver needs to consider F1 cost to be 7.

> [!Warning]
> Ignoring or misusing the constants will result on your application solving a different problem than the one requested, and most probably achieving very low points for the test cases. You have the validator to check beforehand that your system is using them well, use it!

### Need a particular Python package beyond standard ones?

If you use a particular Python project that is not standard, come to us beforehand so we can agree and we can install it in our testing servers. Otherwise your application will crash right away when importing packages.

## Clarifications on constraints

### What are F1 and F2?

In the papers listed in the spec. 👁️

### For cost FC, what is `N`?

If look at the formula in the paper, it only considers up to `N=5`. So there will be a cost for one apart, two apart, three apart, four apart, and five apart. Beyond that, there is no cost. So, it is `N=5`. While the Nottinghan experiments did not consider this metric FC (only F1 and F2), other universities used it.

### Timeslot coincidence

Timeslot coincidence constraint apply _only_ if the exams involved do not any clash, i.e., if they have no common students. If they do clash, then a timeslot coincidence constraint between should be ignored and the exams can be allocated to different time slots.

Note that when a timeslot coincident constraint includes more than two exams, then it means that "all exams in the set should be allocated to the same timeslot", provided no clash at all exist. 😉 If a clash exist between two of the exams in the constraint, then the whole constraint does not apply. If it is important to have particular subsets being coincident then they need to be listed as another separate constraint.

### Enforced timeslots and rooms

Enforced constraints, for timeslots and rooms,  allow a set of possible options: any of the options is OK.

### Combinable Rooms

It is NOT mandatory to combine rooms in a combinable set, but if you do combine them, you must combine all of the rooms in the set (i.e., you cannot combine just a subset of the rooms in the combinable set).

When rooms are combined, their capacities and costs are summed. 🤝

Also, if a set of rooms is used as combinable, then the individual rooms cannot be used separately (i.e., they can only be used as part of the combinable set).

When an exam has room enforcing constraints for rooms that can be combined with others, the enforcing constraint is still respected if allocated to the combinable set that includes the enforced room. 👍

### Global capacity: no. of students or exams?

As per the paper, the global capacity of a slot refers to the number of students that the slot can accommodate. In the classical NOTT problem it was 1550 students per slot, but this can be configured via the `global_capacity` constant. 😉

Note the comment in the source was `% limit of no of exams  per timeslot`, but it should be `% limit of no of students per timeslot`. Thanks for pointing it out! 🙌 We will not push the change as it is a comment and may produce conflicts with your current work in the file. 😉 So you should change these two lines (if you want, not really necessary):

```prolog
#const tmax = #sup.             % max timeslot number that can be used
#const global_capacity = #sup.  % max number of students per timeslot
```

### Impossible exams: what should we do with them?

Your system should allocate ALL exams that have some possible allocation on its own (i.e., without referring to allocation of other exams). Exams that are totally impossible _on their own_, should be _left unscheduled_.

For example, if exam E of size 300 must be run on room R with capacity 200, then your calendar should left exam E unscheduled, as there is no valid allocation for it, even if it was the only exam!

### Timeslot earliness and spread constraints

You should ignore the `timeslot_earliness` and `timeslot_spread` constraints for this project in your solution. Spread and proximity will be covered though other constraints.

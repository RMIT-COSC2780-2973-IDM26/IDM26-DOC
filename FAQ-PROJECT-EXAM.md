# Examination Timetabling Allocation Project - FAQ

- [Examination Timetabling Allocation Project - FAQ](#examination-timetabling-allocation-project---faq)
  - [General](#general)
  - [Respect time-limit!](#respect-time-limit)
  - [Impossible exams!](#impossible-exams)
  - [Respect Clingo constants given!](#respect-clingo-constants-given)
  - [Optional data and default values](#optional-data-and-default-values)
  - [Need a particular Python package beyond standard ones?](#need-a-particular-python-package-beyond-standard-ones)
  - [Validator 🤞](#validator-)
  - [What if the output folder does not exist?](#what-if-the-output-folder-does-not-exist)
  - [Can my application output more than the calendar?](#can-my-application-output-more-than-the-calendar)
  - [Can I change identifiers of exams, rooms, timeslots to lowercase?](#can-i-change-identifiers-of-exams-rooms-timeslots-to-lowercase)
  - [Clarifications on constraints](#clarifications-on-constraints)
    - [What are F1 and F2?](#what-are-f1-and-f2)
    - [Timeslot coincidence](#timeslot-coincidence)
    - [Enforced timeslots and rooms](#enforced-timeslots-and-rooms)
    - [Combinable Rooms](#combinable-rooms)

## General

- Input format may have redundancies, e.g., size of an exam. it is NOT a bug, it is a natural feature of real datasets. Dealing well with this is part of understanding and modeling!

## Respect time-limit!

Your system MUST be able to leave your last best calendar found even if your whole application is terminated, say with a kill command in Linux. There is a  `--time-limit` option built in Clingo and I provided some initial code to propagate it to parameter `timeout` 🕐, so you may want to design your system to track time so far and terminate _gracefully_ before the deadline.

If your system does not output the calendar before being terminated, it will be taken as that it just cannot solve the problem by the autograder.

> [!Caution]
> Leaving the best calendar found so far, and in the right format and filename, is a **minimal requirement**. Teaching staff will not engage in fixing and elaborating your system.

## Impossible exams!

Your system should allocate ALL exams that have some possible allocation on its own (i.e., without referring to allocation of other exams). Exams that are totally impossible _on their own_, should be _left unscheduled_.

For example, if exam E of size 300 must be run on room R with capacity 200, then your calendar should left exam E unscheduled, as there is no valid allocation for it, even if it was the only exam!

## Respect Clingo constants given!

The template provided defines several constants that will be used to configure the type of timetabling requested. Your application MUST respect them 100%. 💯

So if the system is called with option `-allocate_rooms=-1` then we are asking to allocate the rooms, and if option `-prox1_cost 7` is used, your solver needs to consider F1 cost to be 7.

> [!Warning]
> Ignoring or misusing the constants will result on your application solving a different problem than the one requested, and most probably achieving very low points for the test cases. You have the validator to check beforehand that your system is using them well, use it!

## Optional data and default values

Default values should be used on optional data that may not show up in the YAML file of some instances. Defaults are common sense, but they are listed in the documentation within the benchmark folder.

## Need a particular Python package beyond standard ones?

If you use a particular Python project that is not standard, come to us beforehand so we can agree and we can install it in our testing servers. Otherwise your application will crash right away when importing packages.

## Validator 🤞

A separate post was done in [#71](https://edstem.org/au/courses/29085/discussion/3307388) about this.

You can use our validator. Note that there is an initial template code provided under `validator/` in the source code. But this is ONLY if you want to aim for HD level in the end after you have completed Levels 1-3.

Do not work on the validator until you have met all other requirements!

>[!Caution]
> When using the validator, make sure you tell it if you have downcased the identifiers, via option `--downcase`. Check [this question](#can-i-change-identifiers-of-exams-rooms-timeslots-to-lowercase) below.

## What if the output folder does not exist?

The option `--output-dir` tells your solver where to leave your outputs, including the (best) calendar file `calendar.tsz`.

If the folder doesn't exist, your application should create it.

## Can my application output more than the calendar?

YES! And most probably you will want to output more than just the calendar, e.g., some logs, or some intermediate results, or even some other files with different formats. This is totally fine, as long as you also output the calendar in the right format and filename, and leave it in the right folder.

Think about debugging and auditing your runs.

## Can I change identifiers of exams, rooms, timeslots to lowercase?

There are many identifiers in the ETTF, for example, exam IDs, room IDs, student IDs, etc. Different dataset use different IDs, such as numbers (e.g., `23`) or strings (e.g., `STU-8801` or `stu-8801`).

When using Logic Programming, strings starting with capital letters are interpreted as variables, while strings starting with lowercase letters are interpreted as atoms. Therefore, it is important to be consistent with the capitalisation of identifiers, and to ensure that they are correctly interpreted by the validator and the solver.

💁‍♂️ We recommend **dealing with identifiers in their original form**, and quote them as needed to make sure they are dealt as atoms (e.g., `"STU-8801"`) and not as variables.

However, if you want to downcase the identifiers, you can do so, but make sure to be consistent and uniform in doing so:

- Either you downcase _all_ identifiers (e.g., `stu-8801`) or none (e.g., `"STU-8801"`), but do not mix the two styles.
- When downcasing, the whole identifier should be downcased, including the prefix (e.g., `"STU-8801"` should be downcased to `stu-8801`), and the same applies to all identifiers (e.g., exams, rooms, etc).
- Remember that integer 23 is not the same as atom '23'.

If you downcase the identifiers, you should tell so to the validator via option `--downcase`, so that it can correctly interpret the identifiers as atoms.

## Clarifications on constraints

### What are F1 and F2?

In the papers listed in the spec. 👁️

### Timeslot coincidence

Timeslot coincidence constraint apply _only_ if the pair exams involved do not clash, i.e., if they have no common students. If they do clash, then a timeslot coincidence constraint between the two exams should be ignored and the exams can be allocated to different time slots.

### Enforced timeslots and rooms

Enforced constraints, for timeslots and rooms,  allow a set of possible options: any of the options is OK.

### Combinable Rooms

It is NOT mandatory to combine rooms in a combinable set, but if you do combine them, you must combine all of the rooms in the set (i.e., you cannot combine just a subset of the rooms in the combinable set).

When rooms are combined, their capacities and costs are summed. 🤝

Also, if a set of rooms is used as combinable, then the individual rooms cannot be used separately (i.e., they can only be used as part of the combinable set).

When an exam has room enforcing constraints for rooms that can be combined with others, the enforcing constraint is still respected if allocated to the combinable set that includes the enforced room. 👍

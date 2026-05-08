# Examination Timetabling Allocation Project - FAQ

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

## Combinable Rooms

It is NOT mandatory to combine rooms in a combinable set, but if you do combine them, you must combine all of the rooms in the set (i.e., you cannot combine just a subset of the rooms in the combinable set).

When rooms are combined, their capacities and costs are summed. 🤝

Also, if a set of rooms is used as combinable, then the individual rooms cannot be used separately (i.e., they can only be used as part of the combinable set).

When an exam has room enforcing constraints for rooms that can be combined with others, the enforcing constraint is still respected if allocated to the combinable set that includes the enforced room. 👍

## Optional data and default values

Default values should be used on optional data that may not show up in the YAML file of some instances. Defaults are common sense, but they are listed in the documentation within the benchmark folder.

## Need a particular Python package beyond standard ones?

If you use a particular Python project that is not standard, come to us beforehand so we can agree and we can install it in our testing servers. Otherwise your application will crash right away when importing packages.

## Other

- Enforced constraints allow a list of possible options: any of the options is OK.
- Input format may have redundancies, e.g., size of an exam. it is NOT a bug, it is a natural feature of real datasets. Dealing well with this is part of understanding and modeling!

## Validator  🤞

A separate post was done in [#71](https://edstem.org/au/courses/29085/discussion/3307388) about this.

You can use our validator. Note that there is an initial template code provided under `BBvalidator/` in the source code. But this is ONLY if you want to aim for HD level in the end after you have completed Levels 1-3.

Do not work on the validator until you have met all other requirements!

## What are F1 and F2?

In the papers listed in the spec. 👁️

## What if the output folder does not exist?

The option `--output-dir` tells your solver where to leave your outputs, including the (best) calendar file `calendar.tsz`.

If the folder doesn't exist, your application should create it.

## Can my application output more than the calendar?

YES! And most probably you will want to output more than just the calendar, e.g., some logs, or some intermediate results, or even some other files with different formats. This is totally fine, as long as you also output the calendar in the right format and filename, and leave it in the right folder.

Think about debugging and auditing your runs.

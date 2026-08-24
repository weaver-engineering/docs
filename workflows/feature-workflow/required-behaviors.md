# Required Behaviors

## Context
* [Feature Workflow](feature-workflow.md) - the `Analyse The Feature` step this document is the output of
* [Use Cases](use-cases.md) - the usual, but not only, vehicle for stating a required behavior
* [Initial Feature Document](initial-feature-document.md) - the Feature these behaviors are required *of*

## 1 What Required Behaviors Are

Required behaviors are the Analysis-level statement of what a Feature must actually do — the output of `Analyse
The Feature` ([Feature Workflow](feature-workflow.md) §1), before any design work has started. They're the
answer to the Feature's own "wh...s" (who, what, where, when, which, why): concrete enough to say what's needed,
independent of how it ends up being delivered.

## 2 Use Cases Are The Usual Vehicle, Not The Only One

Required behaviors are typically delivered through a number of use cases — each use case's operations (see [Use
Cases §2](use-cases.md)) are where a Feature's required behaviors actually get anchored. A required behavior
doesn't strictly need a use case behind it: it can be stated directly, which is sometimes useful background for
weighing solution options and adjusting requirements to suit the technology landscape without that adjustment
being tracked against any one actor's goal.

That flexibility has a real cost. Without an associated use case, Design has no way of knowing which requirements
might be flexible, or by how much — a use case's actor and goal are what let a later trade-off be checked against
"does this still get the actor to their goal," not just "does this still technically hold." A required behavior
with no use case behind it should be the exception during analysis, not the default.

## 3 Relationship To A Feature's Behaviors

A Feature's required behaviors, once anchored to use case operations, are what eventually become its concrete,
testable behaviors: each operation's logical response, combined with the entry conditions in force when it was
invoked (see [Use Cases §2, §4](use-cases.md)). How that combination gets identified, named, and recorded is
tracked in Linear [WVR-167](https://linear.app/weaver-engineering/issue/WVR-167).

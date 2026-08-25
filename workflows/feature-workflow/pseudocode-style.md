# Pseudocode Style

## Context
* [Use Case Template §Technical Interpretation](../../templates/USE-CASE-TEMPLATE.md) - where solution-independent pseudocode in this style is first written, during Analyse Feature
* [Design Feature Instructions §6, §9.1](design-feature-instructions.md) - where bound pseudocode is recorded and compared against a function's own definition
* [Internal Component Template](../../templates/INTERNAL-COMPONENT-TEMPLATE.md) - where a function's own pseudocode, in this style, is recorded
* [External Dependency Template](../../templates/EXTERNAL-DEPENDENCY-TEMPLATE.md) - where a dependency's declared error modes, referenced by `ON FAILURE` below, are defined
* [Documentation Standards](../../standards/documentation-standards.md) - the document shape this convention follows

Pseudocode gets written at two different points — a use case's Technical Interpretation, during `Analyse Feature`
([Use Case Template](../../templates/USE-CASE-TEMPLATE.md)), and a Service function's own definition, during
`Design Service` ([Design Feature Instructions §6, §9.1](design-feature-instructions.md)) — and design review
depends on comparing the two. That only works if both are written in one shared, disciplined notation: precise
enough to trace mechanically, not so heavy that it tips into committing to an actual programming language. This
document is that notation.

## 1 Vocabulary

A fixed set of keywords; everything else is free text.

* `FUNCTION {name}({params}):` — declares one function or operation's pseudocode.
* `{var} <-- [{target}]` — delegates to another function. The arrow is what marks this as a call rather than
  ordinary computation (`{var} = {expression}`, no brackets, no arrow) — a reader or a mechanical scan can tell
  the two apart without a `CALL` keyword taking up a word of its own.
* `ON FAILURE ({exception class}): {action}` — handles one declared exception class the immediately preceding
  call can raise (§3). Stack more than one where a call can fail more than one distinct way; omit entirely to
  let a call's declared exceptions propagate uncaught.
* `IF {condition}: / ELSE:` — branching, nested by indentation.
* `FOR EACH {item} IN {collection}:` — iteration. Its body follows the same rules as a function's own top-level
  body, `RETURN` included — exiting mid-loop needs no separate `BREAK`.
* `RETURN {value(s)}` — exits the function.
* `RAISE {exception class}` — the function's own logic detects an exceptional condition itself, not from a
  failed call — a divide by zero, an invariant that doesn't hold. This is the third source of unhappy-path
  behaviour [Required Behaviors §4](required-behaviors.md) names: one only visible once the pseudocode is
  written against real components.

## 2 Two Vocabularies, One Style

The same keywords are used at both points pseudocode gets written, but a call's target differs:

* **Technical Interpretation** ([Use Case Template](../../templates/USE-CASE-TEMPLATE.md)) — targets are
  logical capability names, `[{name} - {args}]`. No real Internal Component, External Dependency, or Service is
  named; this pseudocode is solution-independent.
* **A function's own pseudocode** ([Design Feature Instructions
  §6](design-feature-instructions.md), [Design Directory And HLD §4.5](design-directory-and-hld.md)) — targets
  are bound, `[{address}: {name} - {args}]`. The logical name is kept alongside the real address, so the
  pseudocode still reads without cross-referencing what the address actually is.

Worked example — Technical Interpretation:

```
FUNCTION view_own_account(bearer_token):
  identity <-- [resolve_session - bearer_token]
    ON FAILURE (unavailable): RETURN 503, no body
  IF identity IS NOT authenticated:
    RETURN 401
  account <-- [find_account - identity.user_id]
    ON FAILURE (unavailable): RETURN 503, no body
  RETURN 200, account
```

The same operation, bound, as `accounts.interface.view-account`'s own pseudocode once Architect Services and Gap
Analysis have run:

```
FUNCTION accounts.interface.view-account:
  identity <-- [accounts.auth-service.resolve-session: resolve_session - bearer_token]
    ON FAILURE (unavailable): RETURN 503, no body
  IF identity IS NOT authenticated:
    RETURN 401
  account <-- [accounts.accounts-store.find-account: find_account - identity.user_id]
    ON FAILURE (unavailable): RETURN 503, no body
  RETURN 200, account
```

A loop, showing a call and a branch inside `FOR EACH`:

```
FUNCTION orders.interface.place-order:
  FOR EACH item IN order.items:
    stock <-- [orders.inventory-store.check-stock: check_stock - item.sku]
      ON FAILURE (unavailable): RETURN 503, no body
    IF stock < item.quantity:
      RETURN 409, "insufficient stock: " + item.sku
  reference <-- [orders.purchasing-gateway.post-order: post_purchase_order - order]
    ON FAILURE (unavailable): RETURN 503, no body
  RETURN 201, reference
```

## 3 Exception Classes Are Owned By The Callee

An exception class named in `ON FAILURE` is never invented at the call site. It's declared on the callee's own
document: an External Dependency operation's error modes (already required by the [External Dependency
Template](../../templates/EXTERNAL-DEPENDENCY-TEMPLATE.md)), or an Internal Component function's own declared
exceptions. Every caller of the same function references that same fixed vocabulary, rather than each caller
inventing its own name for what's really the same underlying failure.

Handling a declared class is the caller's choice, not an obligation for every one — an exception a function
doesn't catch propagates to whatever called it, the same as ordinary exception semantics.

## 4 Unhandled And Undeclared Is A Design Review Failure

A propagating exception is fine, provided it's declared on whatever function it propagates into's own contract
— that just makes it that function's decision to catch or propagate further. What's not fine is an exception
that's neither caught locally nor declared anywhere along the chain up to the Service's own `interface`: a
failure mode nobody has actually designed a response for, at any level — not "handled ungracefully," genuinely
undecided, which is a direct violation of the broad, blanket graceful-failure requirement Analysis is expected to
have stated ([Required Behaviors §4](required-behaviors.md)).

[Design Feature Instructions §9.1](design-feature-instructions.md) checks this mechanically: walk each
function's pseudocode, collect every exception class it raises itself (`RAISE`) or lets propagate uncaught from
its own calls, and confirm each one is either caught there or added to that function's own declared contract for
whatever calls it. Anything that's neither is a reconciliation failure, not a stylistic gap.

# Rationale

**Why `<--` instead of a `CALL` keyword.** An earlier version used `CALL {name}({args})`, with the keyword doing
the work of distinguishing a call from ordinary computation. The arrow does the same job more compactly, and it
frees `=` to mean plain computation (`total = subtotal + tax`) — a distinction `CALL` alongside `=` for
everything else didn't give as cleanly, since both would have used `=`.

**Why brackets, not markdown links.** `[{target}]` was briefly considered as a real markdown link once a call
target is bound to a real address, since `[text](url)` is already this repo's own link syntax. That was dropped:
a relative path on every call line fights readability exactly where a reader wants to scan the logic fastest,
and the address itself (`accounts.auth-service.resolve-session`) is already enough to navigate by, the same
addressing convention every other document in this system already uses. The brackets are a plain visual
delimiter, not a link.

**Why `ON FAILURE` doesn't repeat the call target.** An early draft wrote `ON FAILURE ([accounts.auth-service.resolve-session] unavailable)`,
repeating the address inside the condition. That's redundant — the call it's handling is already unambiguous
from the immediately preceding line — and confusing, since a bracketed address with no `name - args` inside it
doesn't follow the same grammar every other bracket in this style does. `ON FAILURE` states only the exception
class; which call it belongs to is positional.

**Why exception classes are declared on the callee, not the caller.** Without this, two different callers of the
same function could each invent their own name for what's actually one underlying failure — one calling it
`unavailable`, another `connection_error` — fragmenting a single fact about the dependency into several
inconsistent ones. Declaring it once, on the function or dependency that can actually produce it, and requiring
every caller to reference that fixed vocabulary keeps it one fact, checkable the same way `calls:` and a call
tree are two independent views reconciled by review ([Specific Behaviors §3](specific-behaviors.md)).

**Why unhandled-and-undeclared specifically, not just unhandled.** Letting a *declared* exception propagate
uncaught is a legitimate, deliberate design choice — it means "the function that called this one should decide."
Nothing is lost by not handling it locally, because the fact that it can happen is still on record. An
*undeclared* exception that also goes unhandled has no record anywhere: nobody has decided what should happen,
and nothing in the documented contracts even hints that it's possible. Flagging the combination, not either
condition alone, is what keeps a deliberate propagation decision from being confused with an actual gap.

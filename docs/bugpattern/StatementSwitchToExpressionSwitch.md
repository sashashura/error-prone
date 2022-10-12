We're trying to make `switch` statements simpler to understand at a glance.
Misunderstanding the control flow of a `switch` block is a common source of
bugs.

### Statement `switch` statements:

*   Because of the potential for fall-through, it takes time and cognitive load
    to understand the control flow for each `case`.
*   When a `switch` block is large, just skimming each `case` can be tedious.
*   Fall-though can also be conditional (see example below). In this scenario,
    one would need to think through all possible flows for each `case`. When
    conditionally falling-through multiple `case`s in a row is possible, the
    number of potential control flows can grow rapidly.

### Expression `switch` statements

With an expression `switch` statement, you know at a glance that no cases fall
through. No analysis is needed.

It's also possible to group identical cases together (`case A, B, C`) for
improved readability.

### Examples

``` {.bad}
enum Suit {HEARTS, CLUBS, SPADES, DIAMONDS}

private int foo(Suit suit) {
  switch(suit) {
    case HEARTS:
      return 1;
    case CLUBS:
      // Fall through
    case SPADES:
      // Fall through
    case DIAMONDS:
      bar();
      return 0;
    }
}
```

Which can be simplified into the following expression `switch`:

``` {.good}
enum Suit {HEARTS, CLUBS, SPADES, DIAMONDS}

private int foo(Suit suit) {
  switch(suit) {
    case HEARTS -> return 1;
    case CLUBS, SPADES, DIAMONDS -> {
      bar();
      return 0;
    }
  }
}
```

Here's an example of a complex statement `switch` with conditional fall-through
and complex control flows. How many potential execution paths can you spot?

``` {.bad}
enum Suit {HEARTS, CLUBS, SPADES, DIAMONDS}

private int foo(Suit suit){
  switch(suit) {
    case HEARTS:
      if (bar()) {
        break;
      }
      // Fall through
    case CLUBS:
      if (baz()) {
        return 1;
      } else if (baz2()) {
        throw new AssertionError(...);
      }
      // Fall through
    case SPADES:
      // Fall through
    case DIAMONDS:
      return 0;
  }
  return -1;
}
```

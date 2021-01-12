## 1 

> Show that the terminal object is unique up to unique isomorphism.

Assume _t_ and _t'_ are terminal objects. This means that there is a unique morphism from every element of the collection to both _t_ and _t'_. Specifically:
 * There is a unique morphism _f_ from _t_ to _t'_
 * There is a unique morphism _g_ from _t'_ to _t_
 * There are two identity morphisms _idt_ and _idt'_. Each one is the only morphism going from _t_ or _t'_ to itself.
 
_f_ and _g_ compose into a morphism _f_ ∘ _g_ from _t_ to _t_, but, because _t_ is terminal, and _idt_ is a morphism from _t_ to _t_ itself, _f_ ∘ _g_ == _idt_. Hence _f_ ∘ _g_ is an isomorphism, and _t_ and _t'_ are isomorphic.

Additionally, _f_ is the only morphism from _t_ to _t'_, so this isomorphism is unique.

## 2

> What is a product of two objects in a poset? Hint: Use the universal
  construction.

The product of two objects in a poset is their [meet](https://en.wikipedia.org/wiki/Join_and_meet), if it exists.

**_a_ ∧ _b_ matches the universal construction**. Assume we have two objects _a_ and _b_. Denote their meet as _a_ ∧ _b_. Because _a_ ∧ _b_ ≤ _a_, there is a unique morphism _p_: _a_ ∧ _b_ → _a_, and similarly there is _q_: _a_ ∧ _b_ → _b_.

**_a_ ∧ _b_ is the best such element**. Assume we have an element _c'_ such that:
 * There is a morphism _p'_: _c'_ → _a_ (as we are in a poset, it has to be unique)
 * There is a morphism _q'_: _c'_ → _b_ (also unique)
 
Because there is a morphism from _c'_ to _a_, we know that _c'_ ≤ _a_, and similarly _c'_ ≤ _b_. By definition of the meet, we know that _c'_ ≤ _a_ ∧ _b_, i.e. there is a morphism _m_: _c'_ → _a_ ∧ _b_.

We can compose _m_ and _p_ to get a morphism from _c'_ to _a_. But in a poset, morphisms between any two points are unique if they exist, so by uniqueness _p_ ∘ _m_ = _p'_, and _m_ factors _p'_. By the same argument, _m_ factors _q'_. This shows that _c_ is no better than _a_ ∧ _b_.

## 3

> What is a coproduct of two objects in a poset?

By the exact same reasoning, the coproduct of two objects in a poset is their join.

## 4

> Implement the equivalent of Haskell `Either` as a generic type in
  your favorite language (other than Haskell).

In Rust:

```rust
enum Either<L, R> {
    Left(L),
    Right(R),
}
```
  
## 5

> Show that `Either` is a "better" coproduct than `int` equipped with
  two injections:

  ```c++
  int i(int n) { return n; }
  int j(bool b) { return b? 0: 1; }
  ```

> Hint: Define a function

  ```c++
  int m(Either const & e);
  ```

> that factorizes `i` and `j`.

We can define

```rust
fn m(e: &Either<i64, bool>) -> i64 {
    match e {
        Either::Left(i) => *i,
        Either::Right(b) => if *b { 0 } else { 1 }
    }
}
```

We can also define _i'_ and _j'_, the two injections:

```rust
fn i_prime(n: i64) -> Either<i64, bool> { Either::Left(n) }
fn j_prime(b: bool) -> Either<i64, bool> { Either::Right(n) }
```

Clearly, _i_ = _m_ ∘ _i'_ and _j_ = _m_ ∘ _j'_
  
## 6

> Continuing the previous problem: How would you argue that `int` with
  the two injections `i` and `j` cannot be "better" than `Either`?

For _i_ and _j_ to be better than _i'_ and _j'_, we need to have an _m_ such that _i'_ = _m_ ∘ _i_ and _j'_ = _m_ ∘ _j_. Suppose that one exists.

We have _Left_ 0 = _i'_ 0 = ( _m_ ∘ _i_ ) 0 = _m_ ( _i_ 0 ) = _m_ 0

But also: _Right_ `true` = _j'_ `true` = ( _m_ ∘ _j_ ) `true` = _m_ ( _j_ `true` ) = _m_ 0

Which gives us _Left_ 0 = _Right_ `true`, a contradiction.

## 7

> Still continuing: What about these injections?

  ```c++
  int i(int n) { 
      if (n < 0) return n; 
      return n + 2;
  }
  int j(bool b) { return b? 0: 1; }
  ```

There is complexity here due to the fact that C++ `int`s have finitely many possible values. For `n = INT_MAX`, `n + 2` is not defined by the C++ standard, so _i_ is ill-defined. (Or, if we assume ints wrap around, we end up in a situation similar to the previous question).

To solve this issue, let's assume that `int` types have infinite width (e.g. they are big ints). Then `int` with _i_ and _j_ is as good as `Either` with _i'_ and _j'_.

We already know that `Either` is better than `int`, because `Either` is better than any other coproduct candidate. Let's show that `int` is also better than `Either`. We can define _m_:

```rust
fn m(i: i64) -> Either<i64, bool> {
    match i {
        0 => Either::Right(true),
        1 => Either::Right(false),
        _ if i > 0 => Either::Left(i - 2),
        _ => Either::Left(i),
    }
}
```

It's kind of tedious but easy to verify that _i'_ = _m_ ∘ _i_ and _j'_ = _m_ ∘ _j_. We can just as tediously show that _m_ is unique.

## 8

> Come up with an inferior candidate for a coproduct of `int` and
  `bool` that cannot be better than `Either` because it allows
  multiple acceptable morphisms from it to `Either`.

One example is `(bool, int, bool)`, with 

```rust
fn i(n: i64) -> (bool, i64, bool) { (false, n, false) }
```

and 

```rust
fn j(b: bool) -> (bool, i64, bool) { (true, 0, b) }
```

Here's one m:

```rust
fn m1(triple: (bool, i64, bool)) -> Either<i64, bool> {
    match triple {
        (false, n, _) => Either::Left(n),
        (true, _, b) => Either::Right(b),
    }
}
```

Here's another:

```rust
fn m2(triple: (bool, i64, bool)) -> Either<i64, bool> {
    match triple {
        (false, n, false) => Either::Left(n),
        (false, n, true) => Either::Right(true),
        (true, _, b) => Either::Right(b),
    }
}
```

_m1_ and _m2_ both factor _i'_ and _j'_, but they are not equal. The trick is to make them differ only on values that will never be produced by _i_ or _j_.

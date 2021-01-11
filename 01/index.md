## 1

> Implement, as best as you can, the identity function in your favorite language (or the second favorite, if your favorite language happens to be Haskell).

In Rust:

```rust
fn identity<T>(t: T) -> T {
    t
}
```

## 2

> Implement the composition function in your favorite language. It takes two functions as arguments and returns a function that is their composition.

In Rust, with `FnOnce`s:

```rust
fn compose<F, G, T, U, V>(f: F, g: G) -> impl Fn(T) -> V
    where F: Fn(T) -> U,
          G: Fn(U) -> V {
    move |t: T| g(f(t))
}
```

## 3

> Write a program that tries to test that your composition function respects identity.

The following method tests the identity laws for one function on one argument:

```rust
fn test_identity<F, T: Clone, U: PartialEq>(f: F, t: T) -> bool
    where F: Fn(T) -> U + Clone {
    compose(f.clone(), identity)(t.clone()) == (f.clone())(t.clone())
        && compose(identity, f.clone())(t.clone()) == f(t)
}
```

It can be used as

```rust
assert!(test_identity(|x| x + 3, 5))
```

## 4

> Is the world-wide web a category in any sense? Are links morphisms?

Clearly the pages can be objects and the links can be arrows, but composition is an issue: just because there is a link from _A_ to _B_ and one from _B_ to _C_ doesn't mean there is one from _A_ to _C_. Additionally, each page does not have a link to itself.

However, we could define a new relationship like "has a path to", where a page _A_ has a path to _B_ if either _A_ = _B_, _A_ has a direct link to _B_, or it has a link to some page _A'_, which has a path to _B_. In this case, "has a path to" relations are morphisms.

## 5

> Is Facebook a category, with people as objects and friendships as morphisms?

No, friendship does not compose, and people are not friends with themselves.

## 6

> When is a directed graph a category?

It's a category where the vertices are objects and the edges are morphisms if:

 - Each vertex has an edge from itself to itself
 - For any vertices _A_, _B_, _C_ such that there is an edge _e_: _A_ → _B_ and an edge _e'_: _B_ → _C_, there is an edge _f_: _A_ → _C_.

This is just restating the definition of a category. Also note that any undirected graph can be given category structure by adding arrows where necessary.



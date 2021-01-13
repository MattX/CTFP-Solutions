## 1

> Show the isomorphism between `Maybe a` and `Either () a`.

The morphisms `maybeEither` and `eitherMaybe` are the inverse of each other, so `Maybe a` and `Either () a` are isomorphic.

```
maybeEither :: Maybe a -> Either () a
maybeEither (Just v) = Right v
maybeEither Nothing = Left ()

eitherMaybe :: Either () a -> Maybe a
eitherMaybe (Left ()) = Nothing
eitherMaybe (Right v) = Just v
```

## 2

> Here’s a sum type defined in Haskell:

```haskell
data Shape = Circle Float
           | Rect Float Float
```

> When we want to define a function like `area` that acts on a `Shape`, we do it by pattern matching on the two constructors:

```haskell
area :: Shape -> Float
area (Circle r) = pi * r * r
area (Rect d h) = d * h
```

> Implement `Shape` in C++ or Java as an interface and create two classes: `Circle` and `Rect`. Implement `area` as a virtual function.

I'll do this in Rust to continue with the previous trend. Since there are no interfaces, `area` can't really be a virtual function, but I think doing it with traits is in the spirit of the original exercise.

```rust
trait Shape {
    fn area(&self) -> f32;
}

struct Circle { r: f32 }

impl Shape for Circle {
    fn area(&self) -> f32 { std::f32::consts::PI * self.r * self.r }
}

struct Rect { d: f32, h: f32 }

impl Shape for Rect {
    fn area(&self) -> f32 { self.d * self.h }
}
```

## 3

> Continuing with the previous example: We can easily add a new function circ that calculates the circumference of a Shape. We can do it without touching the definition of Shape:

```haskell
circ :: Shape -> Float
circ (Circle r) = 2.0 * pi * r
circ (Rect d h) = 2.0 * (d + h)
```

> Add `circ` to your C++ or Java implementation. What parts of the original code did you have to touch?

We need to modify the trait:

```rust
trait Shape {
    fn area(&self) -> f32;
    fn circ(&self) -> f32;
}
```

and both trait implementations:

```rust
impl Shape for Circle {
    fn area(&self) -> f32 { std::f32::consts::PI * self.r * self.r }
    fn circ(&self) -> f32 { std::f32::consts::PI * 2 * self.r }
}

impl Shape for Rect {
    fn area(&self) -> f32 { self.d * self.h }
    fn circ(&self) -> f32 { 2 * (self.d + self.h) }
}
```

This is harder than in Haskell, because we need to touch `Shape` and all existing `Shape` implementations. The difference is known as the [expression problem](https://wiki.c2.com/?ExpressionProblem); see the next question for the flip side of this, where Rust has the advantage over Haskell.

## 4

> Continuing further: Add a new shape, `Square`, to `Shape` and make all the necessary updates. What code did you have to touch in Haskell vs. C++ or Java? (Even if you’re not a Haskell programmer, the modifications should be pretty obvious.)

Changes in Rust:

```rust
struct Square { s: f32 }

impl Shape for Square {
    fn area(&self) -> f32 { self.s * self.s }
    fn circ(&self) -> f32 { 4 * self.s }
}
```

We only need to add new definitions relating to `Square`. In contrast, in Haskell, we would have had to change the definitions of both `area` and `circ`, in addition to adding `Square` to `Shape`.

## 5

> Show that `a + a = 2 * a` holds for types (up to isomorphism). Remember that 2 corresponds to Bool, according to our translation table.

The question is to show that `Either a a` and `(bool, a)` are isomorphic for any `a`. The following functions are invertible morphisms back and forth:

```haskell
eitherPair :: Either a a -> (bool, a)
eitherPair (Left v) = (false, v)
eitherPair (Right v) = (true, v)

pairEither :: (bool, a) -> Either a a
pairEither (false, v) = Left v
pairEither (true, v) = Right v
```

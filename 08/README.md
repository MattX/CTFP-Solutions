## 1

> Show that the data type:

```haskell
data Pair a b = Pair a b
```

>is a bifunctor. For additional credit implement all three methods of Bifunctor and use equational reasoning to show that these definitions are compatible with the default implementations whenever they can be applied.

To pause for a moment, the text says

> However, in general, separate functoriality is not enough to prove joint functoriality. Categories in which joint functoriality fails are called premonoidal.

But does't explain what condition needs to hold that's different from functoriality in each argument. nLab has [an example](https://ncatlab.org/nlab/show/funny+tensor+product#separate_functoriality) of a functor in each of two arguments that isn't a bifunctor. I'm going to assume that this is not a concen in the category **Hask**.

We then have

```haskell
instance Bifunctor Pair where
    bimap f g (Pair a b) = Pair (f a) (g b)
    first f (Pair a b) = Pair (f a) b
    second g (Pair a b) = Pair a (g b)
```

Let's verify bifunctor laws:

```haskell
bimap id id (Pair a b) = Pair (id a) (id b) = Pair a b
bimap (f . f') (g . g') (Pair a b) = Pair (f (f' a)) (g (g' b))
                                   = bimap f g (Pair (f' a) (g' b)
                                   = bimap f g (bimap f' g' (Pair a b))
                                   = (bimap f g . bimap f' g') (Pair a b)
```

And for the extra credit:

```haskell
bimap f g (Pair a b) = Pair (f a) (g b)
                     = first f (second g (Pair a b))
                     = (first f . second g) (Pair a b)
first f (Pair a b) = Pair (f a) b = bimap f id (Pair a b)
second g (Pair a b) = Pair a (g b) = bimap id g (Pair a b)
```

## 2

> Show the isomorphism between the standard definition of Maybe and this desugaring:

```haskell
type Maybe' a = Either (Const () a) (Identity a)
```

> Hint: Define two mappings between the two implementations. For additional credit, show that they are the inverse of each other using equational reasoning.

We can define:

```haskell
maybeToMaybe' :: Maybe a -> Maybe' a
maybeToMaybe' Nothing = Left (Const ())
maybeToMaybe' (Just a) = Right (Identity a)

maybe'toMaybe :: Maybe' a -> Maybe a
maybe'toMaybe (Left (Const ())) = Nothing
maybe'toMaybe (Right (Identity a)) = Just a
```

which are obviously inverses of each other.

## 3

> Let’s try another data structure. I call it a `PreList` because it’s a precursor to a `List`. It replaces recursion with a type parameter `b`.

```haskell
data PreList a b = Nil | Cons a b
```

> You could recover our earlier definition of a `List` by recursively applying `PreList` to itself (we’ll see how it’s done when we talk about fixed points).

> Show that `PreList` is an instance of `Bifunctor` .

We can provide an instance:

```haskell
instance Bifunctor PreList where
    bimap f g Nil = Nil
    bimap f g (Cons a b) = Cons (f a) (g b)
```

And verify the laws, which are very similar to the `Pair` case:

```haskell
bimap id id Nil = Nil
bimap id id (Cons a b) = Cons a b

bimap (f . f') (g . g') Nil = Nil = (bimap f g . bimap f' g') Nil
bimap (f . f') (g . g') (Cons a b) = Cons (f (f' a)) (g (g' b))
                                   = (bimap f g . bimap f' g') Cons a b
```

## 4

> Show that the following data types define bifunctors in a and b:

```haskell
data K2 c a b = K2 c
data Fst a b = Fst a
data Snd a b = Snd b
```

> For additional credit, check your solutions agains Conor McBride’s paper [Clowns to the Left of me, Jokers to the Right](http://strictlypositive.org/CJ.pdf).

```haskell
instance Bifunctor K2 where
    bimap f g = id
instance Bifunctor Fst where
    bimap f g (Fst a) = Fst (f a)
instance Bifunctor Snd where
    bimap f g (Snd b) = Snd (g b)
```

Laws:

```haskell
bimap id id (K2 c) = K2 c = id (K2 c)
bimap (f . f') (g . g') (K2 c) = K2 c
                               = (bimap f g . bimap f' g') (K2 c)

bimap id id (Fst a) = Fst (id a) = id (Fst a)
bimap (f . f') (g . g') (Fst a) = Fst (f (f' a))
                                = bimap f g (bimap f' g' (Fst a))

bimap id id (Snd b) = Snd (id b) = id (Snd b)
bimap (f . f') (g . g') (Snd b) = Snd (g (g' b))
                                = bimap f g (bimap f' g' (Snd b))
```

## 5

> Define a bifunctor in a language other than Haskell. Implement `bimap` for a generic pair in that language.

Few languages will let us define an equivalent of the Haskell `Bifunctor` typeclass, because they don't have HKTs. But here's Pair as a bifunctor in Rust:

```rust
struct Pair<A, B>(A, B);

impl<A, B> Pair<A, B> {
    fn bimap<F, G, T, U>(self, f: F, g: G) -> Pair<T, U>
            where F: FnOnce(A) -> T, G: FnOnce(B) -> U {
        Pair(f(self.0), g(self.1))
    }
}
```


## 6

> Should `std::map` be considered a bifunctor or a profunctor in the two template arguments `Key` and `T`? How would you redesign this data type to make it so?

`std::map` is very similar to the `(->)` profunctor, and is also a profunctor.

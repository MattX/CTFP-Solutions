## 1

> Can we turn the Maybe type constructor into a functor by defining:

```haskell
fmap _ _ = Nothing
```

> which ignores both of its arguments? (Hint: Check the functor laws.)

No, because `fmap id (Just ()) = Nothing ≠ Just ()`, so this `fmap` doesn't preserve identity.
    
## 2

> Prove functor laws for the reader functor. Hint: it’s really simple.

For reference, this is the definition of the reader functor in Haskell:

```haskell
instance Functor ((->) r) where
    fmap f g = f . g
```

First, check that `fmap` preserves identity:

```haskell
fmap id g = id . g = g = id g
```

Next, check that `fmap` preserves composition:

```haskell
fmap (g . f) h = (g . f) . h
               = g . (f . h)
               = fmap g (fmap f h)
               = (fmap g . fmap f) h
```

## 3

> Implement the reader functor in your second favorite language (the first being Haskell, of course).

This is a nightmare in Rust, so I'll do it in Python. `value` is expected to be an object of type `Reader r`, aka a method of one argument.

```python
def fmap_functor(f, value):
    return lambda r: f(value(r))
```

## 4

> Prove the functor laws for the list functor. Assume that the laws are true for the tail part of the list you’re applying it to (in other words, use induction).

Instance declaration for reference:

```haskell
instance Functor List where
    fmap _ Nil = Nil
    fmap f (Cons x t) = Cons (f x) (fmap f t)
```

### Identity law

```haskell
fmap id Nil = Nil = id Nil

fmap id (Cons x t) = Cons (id x) (fmap id t)
                   = Cons x (fmap id t)
                   = Cons x t  -- by recurrence hypothesis
                   = id (Cons x t)
```

### Composition law

```haskell
fmap (g . f) Nil = Nil
                 = fmap f Nil
                 = fmap g (fmap f Nil)
                 = (fmap g . fmap f) Nil
fmap (g . f) (Cons x t) = Cons (g (f x)) (fmap (g . f) t)
                        = Cons (g (f x)) (fmap g (fmap f t))  -- by recurrence hypothesis
                        = fmap g (Cons (f x) (fmap f t))
                        = fmap g (fmap f (Cons x t))
                        = (fmap g . fmap f) (Cons x t)
```

## 1
 
> Define a higher-order function (or a function object) `memoize` in your favorite language. This function takes a pure function `f` as an argument and returns a function that behaves almost exactly like `f`, except that it only calls the original function once for every argument, stores the result internally, and subsequently returns this stored result every time it’s called with the same argument. You can tell the memoized function from the original by watching its performance. For instance, try to memoize a function that takes a long time to evaluate. You’ll have to wait for the result the first time you call it, but on subsequent calls, with the same argument, you should get the result immediately.

In Haskell there's no way to hide the state that we need to keep memoized values around, so we have to change the method's type when we memoize it.

```haskell
import Control.Monad.State

main = runState (memoize (\x -> x + 1) 3) []

type Memoized t u = t -> State [(t, u)] u

memoize :: Eq t => (t -> u) -> Memoized t u
memoize f arg = do
    values <- get
    case lookup arg values of
        Just v -> return v
        Nothing -> let v = f arg in do
            put $ (arg, v) : values
            return v
```

## 2

> Try to memoize a function from your standard library that you normally use to produce random numbers. Does it work?

No, because random number generation relies on the side effect of updating an RNG seed. A memoized random number generation function will always return the same number.

## 3

> Most random number generators can be initialized with a seed. Implement a function that takes a seed, calls the random number generator with that seed, and returns the result. Memoize that function. Does it work?

Yes, by explicitly setting the seed every time, we remove the requirement for the RNG to keep mutable state. We now have a deterministic Seed → Number function.
    
## 4

> Which of these C++ functions are pure? Try to memoize them and observe what happens when you call them multiple times: memoized and not.
        
* The factorial function from the example in the text.

Yes

* `std::getchar()`

No, relies on IO side effects.

* ```
  bool f() { 
      std::cout << "Hello!" << std::endl;
      return true; 
  }
  ```

No, relies on IO side effects.

* ```
  int f(int x) {
      static int y = 0;
      y += x;
      return y;
  }
  ```

No, relies on persistent state.

## 5    

> How many different functions are there from Bool to Bool? Can you implement them all?

There are 4 (2^2). They are
 * Constant true
 * Constant false
 * Identity
 * Not
 
In Haskell, there's also `undefined`.

## 6

> Draw a picture of a category whose only objects are the types Void, () (unit), and Bool; with arrows corresponding to all possible functions between these types. Label the arrows with the names of the functions.

In the following diagram, _Kvalue_ is the function that always returns _value_.

![](q6.jpg)

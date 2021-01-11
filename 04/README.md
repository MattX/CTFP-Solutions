> A function that is not defined for all possible values of its argument is called a partial function. It’s not really a function in the mathematical sense, so it doesn’t fit the standard categorical mold. It can, however, be represented by a function that returns an embellished type `optional`:

```c++
template<class A> class optional {
    bool _isValid;
    A    _value;
public:
    optional()    : _isValid(false) {}
    optional(A v) : _isValid(true), _value(v) {}
    bool isValid() const { return _isValid; }
    A value() const { return _value; }
};
```

> As an example, here’s the implementation of the embellished function `safe_root`:

```c++
optional<double> safe_root(double x) {
    if (x >= 0) return optional<double>{sqrt(x)};
    else return optional<double>{};
}
```

> Here’s the challenge:
> 1. Construct the Kleisli category for partial functions (define composition and identity).

* The identity morphism from _A_ to _A_ maps `a` to `optional<A>{a}`;
* Given two morphisms _f: A → B_ and _g: B → C_,
  ```
  (g ∘ f)(x) = g(y)            if f(x) == optional<B>{y}
               optional<C>{}   otherwise
  ```
   
  (I don't really want to write this out in C++).

> 2. Implement the embellished function `safe_reciprocal` that returns a valid reciprocal of its argument, if it’s different from zero.

```c++
optional<double> safe_reciprocal(double x) {
    if (x != 0) return optional<double>{1/x};
    else return optional<double>{};
}
```

> 3. Compose `safe_root` and `safe_reciprocal` to implement `safe_root_reciprocal` that calculates `sqrt(1/x)` whenever possible.

This would be more convincing had I actually implemented `compose_partial`.

```c++
compose_partial(safe_reciprocal, safe_root);
```

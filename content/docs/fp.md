---
Title: Functional Programming
---
## Links

### Free Monad + Interpreter Pattern
- [A Modern Architecture for FP](http://degoes.net/articles/modern-fp)
- [John DeGoes: Beyond Free Monads - λC Winter Retreat 2017](https://www.youtube.com/watch?v=A-lmrvsUi2Y)
- [Why free monads matter](http://www.haskellforall.com/2012/06/you-could-have-invented-free-monads.html)
- [Purify code using free monads](http://www.haskellforall.com/2012/07/purify-code-using-free-monads.html)

### Functional Reactive Programming
- [Reflex: Practical Functional Reactive Programming](https://www.youtube.com/watch?v=dOy7zIk3IUI)
- [Controlling Time and Space: understanding the many formulations of FRP](https://www.youtube.com/watch?v=Agu6jipKfYw)

### Miscellaneous
- [Scott Wlaschin - Railway Oriented Programming — error handling in functional languages](https://vimeo.com/97344498)
## Typeclassopedia

### Functor

The `Functor` class ([haddock](https://hackage.haskell.org/package/base/docs/Prelude.html#t:Functor)) is the most basic and ubiquitous type class in the Haskell libraries. A simple intuition is that a `Functor` represents a “container” of some sort, along with the ability to apply a function uniformly to every element in the container. Another intuition is that a `Functor` represents some sort of “computational context”.

### Definition

Here is the type class declaration for `Functor`:

<pre>
class Functor f where
  fmap :: (a -> b) -> f a -> f b

  (<$) :: a        -> f b -> f a
  (<$) = fmap . const
</pre>

To understand `Functor`, then, we really need to understand `fmap`.

First, the `f a` and `f b` in the type signature for `fmap` tell us that `f` isn’t a concrete type like `Int`; it is a sort of _type function_ which takes another type as a parameter. More precisely, the _kind_ of `f` must be `* -> *`. For example, `Maybe` is such a type with kind `* -> *`: `Maybe` is not a concrete type by itself (that is, there are no values of type `Maybe`), but requires another type as a parameter, like `Maybe Integer`. So it would not make sense to say `instance Functor Integer`, but it could make sense to say `instance Functor Maybe`.

Now look at the type of `fmap`: it takes any function from `a` to `b`, and a value of type `f a`, and outputs a value of type `f b`. From the container point of view, the intention is that `fmap` applies a function to each element of a container, without altering the structure of the container. From the context point of view, the intention is that `fmap` applies a function to a value without altering its context. Let’s look at a few specific examples.

### Laws

<pre>
fmap id = id
fmap (g . h) = (fmap g) . (fmap h)
</pre>

Together, these laws ensure that `fmap g` does not change the _structure_ of a container, only the elements. Equivalently, and more simply, they ensure that `fmap g` changes a value without altering its context.

Unlike some other type classes we will encounter, a given type has at most one valid instance of `Functor`. This [can be proven](http://archive.fo/U8xIY) via the [_free theorem_](http://homepages.inf.ed.ac.uk/wadler/topics/parametricity.html#free) for the type of `fmap`.  In fact, [GHC can automatically derive](http://byorgey.wordpress.com/2010/03/03/deriving-pleasure-from-ghc-6-12-1/) `Functor` instances for many data types.

### Intuition

There are two fundamental ways to think about `fmap`. The first has already been mentioned: it takes two parameters, a function and a container, and applies the function “inside” the container, producing a new container. Alternately, we can think of `fmap` as applying a function to a value in a context (without altering the context).

Just like all other Haskell functions of “more than one parameter”, however, `fmap` is actually _curried_: it does not really take two parameters, but takes a single parameter and returns a function. For emphasis, we can write `fmap`’s type with extra parentheses: `fmap :: (a -> b) -> (f a -> f b)`. Written in this form, it is apparent that `fmap` transforms a “normal” function (`g :: a -> b`) into one which operates over containers/contexts (`fmap g :: f a -> f b`). This transformation is often referred to as a _lift_; `fmap` “lifts” a function from the “normal world” into the “`f` world”.

### Utility functions

* `(<$>)` is defined as a synonym for `fmap`.  This enables a nice infix style that mirrors the `($)` operator for function application. For example, `f $ 3` applies the function `f` to 3, whereas `f <$> [1,2,3]` applies `f` to each member of the list.

---
layout: post
title: Non-Empty Recursion in Elm
highlightjs: true
---

Elm uses total functions, one consequence of which is that functions on lists must handle the empty list. For example, `List.head` has the type `List a -> Maybe a`, since the empty list has no head element. (Haskell, on the other hand, allows partial functions, so `head` has the type `[a] -> a`, and it crashes if you give it an empty list.)

This is great for avoiding bugs but can sometimes be annoying if you *know* that your list is non-empty.

Fortunately, you can just use a non-empty list type such as [`Cons`](http://package.elm-lang.org/packages/hrldcpr/elm-cons/latest/Cons) to avoid both bugs and `Maybe`! For example, `Cons.head` has the type `Cons a -> a`, since non-empty lists always have a head element.

But things can get tricky if you want to recurse on non-empty lists, since the tail of a non-empty list isn't necessarily non-empty. This note talks about how to define non-empty lists in a recursion-friendly way.

## Non-Empty Lists in Terms of Lists

The classic recursive definition of lists in languages like Elm is that they are either the empty list `Nil` or they are a `Cons` of a head element and a tail list:

```elm
type List a = Nil
            | Cons a (List a)
```

Then since Elm functions are total and must handle all cases, the function which gives the head of a list is:

```elm
head : List a -> Maybe a
head list =
  case list of
    Nil -> Nothing
    Cons first rest -> Just first
```

To define a non-empty list type, which we'll call `Cons`, we can just take the definition of `List` from above and remove the `Nil` case:

```elm
type Cons a = Cons a (List a)
```

So now we always have a head, and no longer need Maybe:

```elm
head : Cons a -> a
head (Cons first rest) = first
```

But if we want to implement something recursive, such as the maximum element of a Cons, we run into trouble:

```elm
maximum : Cons a -> a
maximum (Cons first rest) =
  case rest of
    Nil -> first
    _ -> max first (maximum ???)  -- rest is a List, not a Cons,
                                  -- so we can't recurse
```

Since Cons is defined in terms of List, we can't easily recurse on it.

## Non-Empty Lists in Terms of Non-Empty Lists

But there is a way to define a non-empty list recursively: as a head element, *maybe* followed by a tail non-empty list. In other words, the `List a` tail is isomorphic to `Maybe (Cons a)`, where `Nothing` corresponds to the empty list.

So the definition becomes:

```elm
Cons a = Cons a (Maybe (Cons a))
```

Now we can recurse easily:

```elm
maximum : Cons a -> a
maximum (Cons first rest) =
  case rest of
    Nothing -> first
    Just rest -> max first (maximum rest)
```

## In Real Life

If you want to use non-empty lists in Elm, try out my [elm-cons](http://package.elm-lang.org/packages/hrldcpr/elm-cons/latest/Cons) package. Since Elm discourages exposing type constructors, you should use the `uncons'` function to recurse on a Cons:

```elm
maximum : Cons comparable -> comparable
maximum c =
  case uncons' c of
    (first, Nothing) -> first
    (first, Just rest) -> max first (maximum rest)
```

Of course, `maximum` is already defined by the package, and even if it weren't you could use a ["non-empty" fold](http://package.elm-lang.org/packages/hrldcpr/elm-cons/latest/Cons#convenient-folding) to more easily define it:

```elm
maximum : Cons comparable -> comparable
maximum = foldl1 max
```

You can read more about the recursive representation of Cons in the ["List May Be Cons"](http://package.elm-lang.org/packages/hrldcpr/elm-cons/latest/Cons#list-may-be-cons) section of the documentation.

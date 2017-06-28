---
layout: post
author: Xuanrui
title: Basic type theory, I guess - why there aren't real products in ML, or any real, CBV, eager language
location: Eugene, OR
---

Hi, everyone, from the University of Oregon! In my last post, I said I would be at OPLSS, and now here I am.
The ideas in this blog post come from [Dan Licata](http://dlicata.web.wesleyan.edu/)'s lecture at OPLSS 2017.
We all know that product types are pairs and sum types are enums, I guess. In fact, we all know that ML has
algebraic data types, which are supposedly a combination of $$+$$ and $$\times$$. For products we also have
tuples, with `(a, b)` being an introduction form and `fst` & `snd` elimination forms. Now, why do I still
say ML has no real product types?

Consider the function below, in my favorite language, OCaml:
```ocaml
let rec bottom x = bottom x
```
We all know this function will never terminate; the computation will go on forever without giving a value
back to us. `bottom` has the type $$\forall \rho \ . \ \forall \tau \ . \ \rho \rightarrow \tau$$; so,
if we give it an argument of any type (say we type in `bottom 42`, so we give it an `int`), it will
give us a value of type $$\forall \tau \ . \ \tau$$.

But `bottom` never returns, so how could this be right? Remember, though, that each type $$\tau$$ has a "bottom"
value $$\bot_{\tau}$$ which represents computations that couldn't succeed, e.g., never terminate, throw an
exception, format the entire hard drive half-way, or cause the computer to explode, etc. Thus, we could say that
`bottom` returns $$\bot_{\tau}$$, where $$\tau$$ is your favorite type.

Now, let us look at the small-step operational semantics for `fst`:  
![Projection Rule]({{ site.baseurl }}/images/proj-rule.png)

and then consider, for example, the expression
```ocaml
fst (1, bottom 42)
```
`bottom 42` has a value of $$\bot$$ and an unspecified (i.e., whatever you'd like) type, or
to be formal, the type $$\forall \tau \ . \ \tau$$, so obviously it's a value. We could, thus,
apply our rule and conclude that this expression should give us 1.

But does it? Instead of giving us "1", OCaml enters an infinite loop, hence we got
$$\bot$$. What does it tell us? OCaml and SML (or basically any eager dialect ML)
are doing products WRONG! Therefore, there aren't real product types in ML.

On a side note, although ML is doing products wrong, coproducts/sums are done right.
So we could take a breath use them safely. Moreover, although products aren't exactly
done right, they probably have little implication to OCaml programmers; so, if you're
not a programming languages researcher or type theorist, don't let this bother you.

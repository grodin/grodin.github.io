---
layout: post
title: "Exponential Backoff in RxJava"
description: "Writing an RxJava Operator to implement exponential backoff"
category:
tags: [rxjava]
---

I recently implemented a fairly simple version of exponential backoff which will
work for any `Observable` in [RxJava][1], and I thought it would make a nice
little post to write about it.

I'm assuming that anyone reading this already grasps the basics of RxJava, so I
won't explain any of that. If not, there's a ton of really good material on the
[ReactiveX website][2], and it's not hard to find plenty of good blog posts by
just googling.

### Retrying Observables

So the problem I'm trying to solve in this post is: how to automatically retry an
`Observable` (i.e. re-subscribe to it) when it fails, and how to do so repeatedly
with an increasing delay between each attempt? An obvious use-case for this is in
network calls **_Add more explanation here?_**.

An `Observable` signals failure by calling `onError()` on it's downstream
`Subscriber`, so what we want to do is to respond to that call and conditionally
resubscribe to the `Observable` (to keep things clear in what comes, I'm going to
refer to the `Observable` which we're retrying as the **_source_** `Observable`).

Luckily for us, RxJava already provides a method which already does exactly what
we're looking for: `retryWhen()` ([JavaDoc][3]). It's slightly tricky to
understand, but the JavaDoc does have a good example, and I'm going to do my best
to explain it.

### retryWhen

The JavaDoc for `retryWhen()` shows that it takes a parameter with the type

```java
Func1<? super Observable<? extends java.lang.Throwable>,? extends Observable<?>>
```

which looks like `Func1<Observable<Throwable>>,Observable<?>>`, ignoring most of
the wildcards. We'll call this function the **_notification handler_**, after the
parameter's name.

If the **source** `Observable` calls `onNext()` or `onComplete()`, those calls are
simply passed downstream. If the **source** calls `onError(Throwable)`, that
`Throwable` is wrapped up into an `Observable` stream, and passed to the
**notification handler**. The **notification handler** then has to return an
`Observable<?>`, i.e. an `Observable` of _any_ type, which we'll call the
**_signal_** `Observable`.

It's the **signal** `Observable` that tells `retryWhen()` what to do. If the
**signal**  calls `onNext()`, then `retryWhen()` will resubscribe to the
**source** and pass any `onNext()` or `onComplete()` calls from _that_ downstream,
just as before. If the **signal** calls `onError()` or `onComplete()` then
`retryWhen()` just passes those straight downstream, not touching the source
again.

So in order to use `retryWhen()` we need to put our logic deciding whether to
resubscribe to the source in the notification handler, signalling our decision by
means of an `Observable`.

### Exponential backoff

So here's a first implementation of the notification handler. I'm using Java 8
syntax, because it's _so_ much shorter.

```java
throwables -> {
    return throwables
        .zipWith(Observable.range(0, maxRetries + 2), (t,i) -> i)
        .flatMap(i -> {
            return Observable.timer(i, TimeUnit.MILLISECONDS);
        });
    }
```

[1]: https://github.com/ReactiveX/RxJava
[2]: http://reactivex.io/tutorials.html
[3]: http://reactivex.io/RxJava/javadoc/rx/Observable.html#retryWhen%28rx.functions.Func1%29
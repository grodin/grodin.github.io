+++
date = 2024-03-20
title = "Unfolding or how to build a Stream (or Iterator)"
+++
I've been playing with async rust recently, and in particular with [`Stream`s][stream] (also called [`AsyncIterator`][async-iterator] in the unstable stdlib implementation). I think they work really well, and the stdlib name really encompasses what they are IMO. They're just `Iterator`s which work asynchronously!

The main point of this post though is really just about one thing I learned recently, about how to build a `Stream` without creating a struct and writing an `impl`.

The key is the function [`unfold`][futures-unfold], found in the [futures crate][futures]. 

It's signature is this:

```rust
pub fn unfold<T, F, Fut, Item>(init: T, f: F) -> Unfold<T, F, Fut>
where
    F: FnMut(T) -> Fut,
    Fut: Future<Output = Option<(Item, T)>>,
```

where `Future` is the [trait in the stdlib][stdlib-future]. I'm going to assume that you're familiar with `Future`s and how to use them for the rest of this post.

From the signature we can see that unfold takes a value `init` and an async closure `FnMut(T) -> Option<(Item, T)>`. The trick to understanding unfold is that inside the closure, we need to implement a state machine. Then unfold drives that state machine until it completes. 

Let's look at the example given in the `futures` crate of how to use `unfold`:

```rust
use futures::stream::{self, StreamExt};

let stream = stream::unfold(0, |state| async move {
    if state <= 2 {
        let next_state = state + 1;
        let yielded = state * 2;
        Some((yielded, next_state))
    } else {
        None
    }
});

let result = stream.collect::<Vec<i32>>().await;
assert_eq!(result, vec![0, 2, 4]);
```

### StreamExt? Which StreamExt?

The first thing to note is that `StreamExt` is the [extension trait][futures-streamext] for `Stream`s, again from `futures`. Unlike `Iterator`, `Stream` has only one provided method (`size_hint()`) and all the useful methods are instead defined on the `StreamExt` trait. 

I'm not sure why this design choice was made, but one result of this is that there are in fact (at least) _two_ `StreamExt` traits in the async ecosystem; one is in `futures` as in this case, and there's [another][tokio-streamext] from the `tokio` ecosystem . This is quite inconvenient when you're using both `tokio` and `futures` in the same crate, and your IDE automatically inserts `use` statements. You often end up with both `StreamExt`s imported, and get a compilation error as a result.

In any case, the only use of `StreamExt` in the example snippet above is to provide the `collect()` method.

### Back to the main story

The next thing to realise is that the closure is pretty clearly implementing a state machine. The first time the closure is called, `state` is `0`, so we enter the first branch of the `if`, compute the next state and the value to be output by the stream, and return them as a pair wrapped in `Some`. 

This is also the trick by which the closure is `FnMut` (i.e. it potentially mutably borrows it's state), since we hand back the state or a new version of it when the closure returns. If we didn't give the state back, the closure could only be `FnOnce` or we couldn't `move` anything into it, severely limiting what we can do.








[stream]: https://docs.rs/futures/0.3.30/futures/stream/trait.Stream.html
[async-iterator]: https://doc.rust-lang.org/std/async_iter/trait.AsyncIterator.html
[futures-unfold]: https://docs.rs/futures/0.3.30/futures/stream/fn.unfold.html
[futures]: https://docs.rs/crate/futures/0.3.30
[stdlib-future]: https://doc.rust-lang.org/std/future/trait.Future.html
[futures-streamext]: https://docs.rs/futures/latest/futures/stream/trait.StreamExt.html
[tokio-streamext]: https://docs.rs/tokio-stream/latest/tokio_stream/trait.StreamExt.html
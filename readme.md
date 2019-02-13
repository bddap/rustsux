# A list of things I dislike about Rust

## derive(Clone) is not reliable

Trait bounds for generic types are generated incorrectly. For example:

```rust
#[derive(Clone)]
struct Foo<T> {
	inner: PhantomData<T>,
}
```

The generated code unnecesarily requires `T` to be clone. `PhantomData<T>` is the what should be required to implement clone in this case.

What's worse, when `T` is not Clone, the derive fails silently. Rustc won't emit any errors until a `.clone()` is called on a `Foo` object.
The silent errors are confusing, and end up wasting a lot of time.

https://github.com/rust-lang/rust/issues/26925

## `cargo check` does not check doctests

## negative trait bounds are not implemented

The following code is not currently possible:

```rust
trait CheckSend {
	fn is_send() -> bool;
}

impl<T: Send> CheckSend for T {
	fn is_send() -> bool {
		true
	}
}

impl<T: !Send> CheckSend for T {
	fn is_send() -> bool {
		false
	}
}
```

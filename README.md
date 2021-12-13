# swift-bridge [![Actions Status](https://github.com/chinedufn/swift-bridge/workflows/test/badge.svg)](https://github.com/chinedufn/swift-bridge/actions) [![docs](https://docs.rs/swift-bridge/badge.svg)](https://docs.rs/swift-bridge)

> Call Rust from Swift and vice versa. 

`swift-bridge` generates code that helps you call Swift from Rust and vice versa.

_`swift-bridge` takes inspiration from the bridge module idea pioneered by [cxx](https://github.com/dtolnay/cxx)._

## Current Status

`swift-bridge` is not yet production ready.

We need to use it more before we can be confident enough in the generated FFI glue to call `swift-bridge` production ready.

Right now I'm looking for feedback in order to continue to improve the APIs and the generated code.

I can especially use feedback from people with Swift experience, since I don't have much.

## Installation

```toml
# In your Cargo.toml

[build-dependencies]
swift-bridge-build = "0.1"

[dependencies]
swift-bridge = "0.1"
```

## Quick Peek

Here's a quick peek at how bindings look.

A more thorough walk through of `swift-bridge` can be found in the book (TODO: Link to GitHub pages).

```rust
// lib.rs

#[swift_bridge::bridge]
mod ffi {
    // Shared types 
    #[swift_bridge(swift_repr = "struct")]
	struct ASharedStruct {
	    field: u32
	}

    // Exposes super::ARustStack to Swift.
    extern "Rust" {
        type ARustStack;

        fn push (&mut self, val: u8);

        fn pop (&mut self) -> Option<u8>;
      
        fn as_slice (&self) -> &[u8];

        fn do_stuff(override: Option<u8>);
    }

    // Exposes a Swift class to Rust.
    extern "Swift" {
        type SwiftApiClient;

        #[swift_bridge(init)]
        fn new_with_timeout(timeout: u8) -> SwiftApiClient;

        #[swift_bridge(associated_to = FileSystemClient)]
        fn version () -> String;

        fn post_bytes(&self, bytes: &[u8]);
    }
}
```

## TODO's before open sourcing

- Create a tutorial on setting up a project

## Built-In Types

`swift_bridge` comes with support for a number of Rust and Swift standard library types.

| name in Rust                                                    | name in Swift                                                    | notes                                                                                                                                                                               |
| ---                                                             | ---                                                              | ---                                                                                                                                                                                 |
| u8, i8, u16, i16... etc                                         | UInt8, Int8, UInt16, Int16 ... etc                               |                                                                                                                                                                                     |
| bool                                                            | Bool                                                             |                                                                                                                                                                                     |
| String, &String, &mut String                                    | RustString                                                       |                                                                                                                                                                                     |
| &str                                                            | RustStr                                                          |                                                                                                                                                                                     |
| Vec<T>                                                          | RustVec\<T>                                                      |                                                                                                                                                                                     |
| SwiftArray\<T>                                                  | Array\<T>                                                        | Not yet implemented                                                                                                                                                                 |
| &[T]                                                            | UnsafeBufferPointer\<T>                                          |                                                                                                                                                                                     |
| &mut [T]                                                        | UnsafeMutableBufferPointer\<T>                                   | Not yet implemented                                                                                                                                                                 |
| SwiftString                                                     | String                                                           |                                                                                                                                                                                     |
| Box<T>                                                          |                                                                  | Not yet implemented                                                                                                                                                                 |
| [T; N]                                                          |                                                                  | Not yet implemented                                                                                                                                                                 |
| *const T                                                        | UnsafePointer\<T>                                                |                                                                                                                                                                                     |
| *mut T                                                          | UnsafeMutablePointer\<T>                                         |                                                                                                                                                                                     |
| Option\<T>                                                      | Optional\<T>                                                     | Currently only supported for primitive function return types.<br /> Other places such as function arguments are  not yet implemented.<br /> Non primitive T is not yet implemented. |
| Result\<T>                                                      |                                                                  | Not yet implemented                                                                                                                                                                 |
| Have a Rust standard library type in mind?<br /> Open an issue! |                                                                  |                                                                                                                                                                                     |
|                                                                 | Have a Swift standard library type in mind?<br /> Open an issue! |                                                                                                                                                                                     |

## To Test

To run the test suite.

```sh
# Clone the repository
git clone git@github.com:chinedufn/swift-bridge.git
cd swift-bridge

# Run tests
cargo test --all && ./test-integration.sh
```

## See Also

- [Rust on iOS](https://mozilla.github.io/firefox-browser-architecture/experiments/2017-09-06-rust-on-ios.html)
  - A blog post by Mozilla that explains how to run Rust on iOS.

# coroutine-rs

[![Build Status](https://img.shields.io/travis/rustcc/coroutine-rs.svg)](https://travis-ci.org/rustcc/coroutine-rs) [![crates.io](https://img.shields.io/crates/v/coroutine.svg)](https://crates.io/crates/coroutine) [![crates.io](https://img.shields.io/crates/l/coroutine.svg)](https://crates.io/crates/coroutine)

Coroutine library in Rust

```toml
[dependencies.coroutine]
git = "https://github.com/rustcc/coroutine-rs.git"
```

or

```toml
[dependencies]
coroutine = "*"
```

## Usage

Basic usage of Coroutine

```rust
extern crate coroutine;

use coroutine::Coroutine;

fn main() {
    // Spawn a new coroutine
    let coro = Coroutine::spawn(move|| {

        println!("1. Hello in coroutine!");

        // Yield back to it's parent
        Coroutine::sched();

        println!("3. We are back!!");

        // Spawn a new coroutine
        Coroutine::spawn(move|| {
            println!("4. Begin counting ...");
            for i in 0..5 {
                println!("Counting {}", i);

                // Yield to it's parent
                Coroutine::sched();
            }
            println!("5. Counting finished");
        }).join().ok().expect("Failed to join");

        println!("6. Good bye");
    });

    // Resume `coro`
    coro.resume().ok().expect("Failed to resume");

    println!("2. We are here!");

    // Resume the coroutine
    coro.resume().ok().expect("Failed to resume");

    println!("7. Back to main.");
}
```

This program will print the following to the console

```
1. Hello in coroutine!
2. We are here!
3. We are back!!
4. Begin counting ...
Counting 0
Counting 1
Counting 2
Counting 3
Counting 4
5. Counting finished
6. Good bye
7. Back to main.
```

## Goals

- [x] Basic single threaded coroutine support

- [x] Clonable coroutine handle

- [ ] Thread-safe: can only resume a coroutine in one thread simultaneously

- [ ] Multithreaded scheduler with work stealing feature

- [ ] Asynchronous I/O supports

## Notes

* Currently this crate **can only** be built with Rust nightly because of some unstable features.

* Basically it supports arm, i686, mips, mipsel and x86_64 platforms, but we have only tested in

    - OS X 10.10.*, x86_64

    - ArchLinux, x86_64

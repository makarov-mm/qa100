# Rust Junior Interview Questions

Question-answer interview preparation file for Rust at Junior level.

## Question 001

### Question
What is Rust?

### Answer
Rust is a statically typed, compiled systems programming language focused on three goals at once: memory safety, performance comparable to C and C++, and fearless concurrency. Its defining feature is that it guarantees memory safety at compile time through an ownership and borrowing model, without using a garbage collector. This means a large class of bugs — use-after-free, double free, null dereferences, and data races — are caught by the compiler rather than crashing at runtime. Rust is used for operating systems, embedded devices, game engines, command-line tools, web backends, and WebAssembly, anywhere you want low-level control with strong safety guarantees.

## Question 002

### Question
What problems is Rust designed to solve?

### Answer
Rust targets the long-standing trade-off in systems programming where you could have either control and speed (C/C++) or safety and convenience (managed languages with a garbage collector), but not both. C and C++ give full control over memory but make it easy to introduce undefined behavior such as buffer overflows and dangling pointers. Managed languages remove those bugs but add a runtime and a garbage collector that costs predictability and performance. Rust's answer is to move the safety checks to compile time via the borrow checker, so you get manual-memory-management performance with automatic-memory-safety guarantees and no runtime garbage collection.

## Question 003

### Question
Does Rust have a garbage collector?

### Answer
No. Rust does not use a garbage collector; memory is managed deterministically through ownership. Each value has a single owner, and when the owner goes out of scope the value is automatically freed at a precise, known point in the code. This is the RAII pattern (Resource Acquisition Is Initialization) enforced by the language, so cleanup is predictable and there are no unpredictable GC pauses. The cost of this approach is that you must satisfy the borrow checker's rules at compile time, which has a learning curve, but the payoff is no runtime overhead for memory management.

## Question 004

### Question
What is Cargo?

### Answer
Cargo is Rust's official build system and package manager. It handles compiling your code, downloading and building dependencies (called crates) from the registry crates.io, running tests, generating documentation, and producing release builds. Common commands are `cargo new` to create a project, `cargo build` to compile, `cargo run` to build and run, `cargo test` to run tests, and `cargo build --release` for an optimized build. Cargo reads project configuration from a `Cargo.toml` file and records exact resolved dependency versions in `Cargo.lock`, which is what makes Rust builds reproducible.

## Question 005

### Question
What is a crate?

### Answer
A crate is the fundamental unit of compilation and code distribution in Rust. There are two kinds: a binary crate, which compiles to an executable and has a `main` function, and a library crate, which compiles to code meant to be used by other crates and has no `main`. When people say they are "adding a crate" they usually mean adding a library dependency from crates.io. The crate is also the unit of privacy and the root of the module tree, so understanding crates is key to understanding how Rust code is organized and shared.

## Question 006

### Question
What is the difference between a package and a crate?

### Answer
A package is a bundle of one or more crates plus a `Cargo.toml` that describes how to build them. A crate is a single compilation unit (one binary or one library). A package can contain at most one library crate but any number of binary crates, which is why a project can ship a library plus several command-line tools together. In everyday use the distinction is subtle: when you run `cargo new`, you get a package containing one crate. The terms are often used loosely, but interviewers may check that you know a package is the Cargo-level concept and a crate is the compiler-level concept.

## Question 007

### Question
What is a module in Rust?

### Answer
A module is a way to organize code within a crate into named, nested namespaces that also control privacy. You declare a module with the `mod` keyword, and items inside it (functions, structs, other modules) are private by default, becoming visible to the outside only when marked `pub`. Modules let you group related functionality and create clear boundaries, and they form a tree rooted at the crate. A common beginner confusion is that modules are not the same as files: a module can live in its own file, but the module structure is logical and declared with `mod`, independent of the filesystem layout.

## Question 008

### Question
How does the `use` keyword work?

### Answer
The `use` keyword brings a path into scope so you can refer to an item by a shorter name instead of writing its full path every time. For example, `use std::collections::HashMap;` lets you write `HashMap` rather than `std::collections::HashMap` throughout the file. It does not move or copy code; it only creates a local shortcut, similar to an import in other languages. You can rename with `as` (`use std::io::Result as IoResult;`), bring in multiple items with braces (`use std::cmp::{min, max};`), and re-export with `pub use` so that callers of your module can access the item through your path.

## Question 009

### Question
What is the difference between a binary crate and a library crate?

### Answer
A binary crate produces a runnable executable and must contain a `main` function as its entry point; its source root is conventionally `src/main.rs`. A library crate produces reusable code that other crates depend on, has no `main`, and its source root is conventionally `src/lib.rs`. Libraries expose a public API through `pub` items, while binaries are the programs that consume libraries and do something useful. Many real projects split logic into a library crate (which is easy to test and reuse) and keep a thin binary crate that just wires the library to the command line.

## Question 010

### Question
What is the `main` function?

### Answer
`main` is the entry point of a binary crate — the function the program starts executing when it runs. In its simplest form it takes no arguments and returns nothing (`fn main() {}`). It can also return a `Result`, for example `fn main() -> Result<(), Box<dyn Error>>`, which lets you use the `?` operator inside `main` and have the program exit with an error code if it returns `Err`. Only binary crates have a `main`; library crates do not, because they are meant to be called by other code rather than run directly.

## Question 011

### Question
What is a variable and how do you declare one?

### Answer
A variable is a named binding to a value, declared with the `let` keyword, for example `let x = 5;`. Rust infers the type when it can, so you rarely need to annotate, but you can be explicit with `let x: i32 = 5;`. By default a binding is immutable, meaning once set it cannot be reassigned. Rust strongly distinguishes the binding (the name) from the value it holds, and much of the language's safety comes from tracking how each binding owns or borrows its value.

## Question 012

### Question
Why are variables immutable by default in Rust?

### Answer
Immutability by default makes code easier to reason about and prevents a whole class of bugs where a value changes unexpectedly. When a binding cannot change, the compiler and the reader both know its value is stable, which is especially valuable for concurrency, since immutable data can be shared safely across threads. If you do need to mutate, you opt in explicitly with `mut`, making mutation visible and intentional rather than the silent default. This default reflects Rust's general philosophy of making the safe, predictable choice the easy one and requiring you to ask for the more powerful, riskier behavior.

## Question 013

### Question
What does the `mut` keyword do?

### Answer
`mut` makes a binding mutable, allowing you to reassign or modify its value after declaration: `let mut x = 5; x = 6;`. Without `mut`, attempting to change the value is a compile error. The keyword applies to the binding, and it also matters for references: `&mut x` is a mutable reference that allows the borrower to change the value it points to. Marking only what truly needs to change as `mut` keeps the rest of your code immutable and easier to reason about, and the compiler will even warn you if you declare something `mut` but never mutate it.

## Question 014

### Question
What is shadowing?

### Answer
Shadowing is declaring a new variable with the same name as an existing one using `let` again, which creates a brand-new binding that hides the previous one in the current scope. For example, `let x = 5; let x = x + 1; let x = x * 2;` ends with `x` equal to 12. Crucially, shadowing can change the type, which is its main advantage: you can write `let spaces = "   "; let spaces = spaces.len();` to go from a string to a number while reusing the name. Because each `let` is a new binding, shadowing does not require `mut` and does not actually mutate the original value.

## Question 015

### Question
What is the difference between shadowing and `mut`?

### Answer
Both let you "change" the value associated with a name, but they work differently. `mut` mutates the same binding in place and requires the type to stay the same, so `let mut x = 5; x = "hi";` is an error. Shadowing creates a new, independent binding with `let`, so it can change the type and even temporarily reuse a name for a transformed value. Shadowing also keeps the new value immutable unless you add `mut` again. In short, use `mut` when you genuinely need an in-place mutable value, and use shadowing when you want to transform a value (possibly changing its type) while reusing a convenient name.

## Question 016

### Question
What is a constant in Rust?

### Answer
A constant is a value bound with the `const` keyword that is fixed at compile time and can never be mutable, for example `const MAX_POINTS: u32 = 100_000;`. Constants require an explicit type annotation, can be declared in any scope including the global scope, and may only be set to a constant expression, not the result of a runtime computation. They are conventionally named in SCREAMING_SNAKE_CASE. Because they are inlined wherever used and have no fixed memory location, they differ from `static` variables, which do have a single fixed address.

## Question 017

### Question
What is the difference between `const` and `static`?

### Answer
Both define values that live for the program's lifetime, but they differ in how they are stored and used. A `const` is a compile-time constant that gets inlined at each use site, so it has no single memory address and effectively behaves like a named literal. A `static` is a single value with a fixed memory location that exists for the entire run of the program (the `'static` lifetime), so references to it are stable. Statics can be mutable (`static mut`), but accessing a mutable static is `unsafe` because it can cause data races. As a rule, prefer `const` unless you specifically need a single fixed address or a global mutable value.

## Question 018

### Question
What are the scalar types in Rust?

### Answer
Scalar types represent single values, and Rust has four categories: integers, floating-point numbers, booleans, and characters. Integers come in signed (`i8` through `i128` and `isize`) and unsigned (`u8` through `u128` and `usize`) variants. Floats are `f32` and `f64`, with `f64` being the default. The boolean type `bool` holds `true` or `false`. The character type `char` is a four-byte Unicode scalar value, so it can hold far more than ASCII. These scalar types are all `Copy`, meaning assigning them copies the value rather than moving it.

## Question 019

### Question
What integer types does Rust have and how does overflow behave?

### Answer
Rust has signed integers `i8`, `i16`, `i32`, `i64`, `i128`, and pointer-sized `isize`, plus unsigned `u8` through `u128` and `usize`. The default integer type is `i32`. Overflow behavior depends on the build profile: in debug builds, arithmetic overflow causes a panic so bugs surface early, while in release builds it wraps around using two's complement by default for performance. Because relying on either is usually a mistake, Rust provides explicit methods like `wrapping_add`, `checked_add` (returns `Option`), `saturating_add`, and `overflowing_add` so you can state exactly which behavior you intend.

## Question 020

### Question
What are the floating-point types?

### Answer
Rust has two floating-point types: `f32` (single precision) and `f64` (double precision), both following the IEEE 754 standard. The default is `f64` because on modern hardware it is roughly as fast as `f32` while offering more precision. Floating-point numbers can represent fractional values but, as in every language, they cannot represent all decimals exactly, so comparisons should account for rounding and you should never use `==` to compare results of floating-point arithmetic blindly. For money or exact decimal needs, you would reach for a dedicated decimal crate rather than `f64`.

## Question 021

### Question
What are the boolean and character types?

### Answer
The boolean type `bool` has exactly two values, `true` and `false`, and is used in conditions and logical operations; unlike some languages, Rust does not treat integers as truthy or falsy, so conditions must be actual booleans. The character type `char` represents a single Unicode scalar value and is four bytes in size, written with single quotes like `'a'`, `'€'`, or `'😀'`. Because `char` is a Unicode scalar value rather than a byte, it can hold characters from any language, which is part of why Rust strings are UTF-8 and cannot be indexed by simple integer position.

## Question 022

### Question
What is a tuple?

### Answer
A tuple is a fixed-size collection that groups together a number of values of possibly different types, written with parentheses like `let point = (1, 2.5, "x");`. You access its elements either by destructuring (`let (a, b, c) = point;`) or by index with a dot (`point.0`, `point.1`). Tuples are useful for returning multiple values from a function without defining a struct. The empty tuple `()`, called the unit type, represents "no meaningful value" and is what functions return when they have no explicit return value.

## Question 023

### Question
What is an array?

### Answer
An array is a fixed-size collection of elements that all have the same type, stored contiguously, written like `let a = [1, 2, 3, 4, 5];` with the type `[i32; 5]`. The length is part of the type and is known at compile time, so arrays cannot grow or shrink. You access elements by index (`a[0]`), and Rust checks bounds at runtime, panicking on out-of-range access rather than reading invalid memory. Arrays live on the stack and are best when you know the exact number of elements ahead of time; when you need a growable sequence, you use a `Vec` instead.

## Question 024

### Question
What is the difference between an array and a `Vec`?

### Answer
An array `[T; N]` has a fixed length known at compile time and is typically stored on the stack, while a `Vec<T>` is a growable, heap-allocated list whose length can change at runtime. Because an array's size is part of its type, you cannot add or remove elements, whereas a `Vec` supports `push`, `pop`, and dynamic resizing. Arrays are cheaper and simpler when the count is fixed and small; `Vec` is the general-purpose choice when you do not know the size in advance or need it to change. Both provide bounds-checked indexing and can be sliced.

## Question 025

### Question
What is a `String` in Rust?

### Answer
`String` is an owned, growable, heap-allocated UTF-8 text type from the standard library. Because it owns its data, you can modify it, append to it with `push_str`, and it is freed automatically when it goes out of scope. It is created in ways like `String::from("hello")` or `"hello".to_string()`. Internally a `String` is essentially a `Vec<u8>` guaranteed to hold valid UTF-8, which is why you cannot index it by a simple integer to get a character — a byte position is not the same as a character position in UTF-8. For text you own and may change, you use `String`; for borrowed views you use `&str`.

## Question 026

### Question
What is `&str`?

### Answer
`&str`, called a string slice, is a borrowed, immutable view into UTF-8 text that someone else owns. It is a reference plus a length, pointing at data that may live in a `String`, in the program's static memory (string literals like `"hello"` are `&'static str`), or anywhere else. Because it only borrows, it does not own or free the data and cannot grow. Functions that just need to read text should take `&str` rather than `String`, because a `&str` can accept literals, slices of a `String`, and more, making the function maximally flexible.

## Question 027

### Question
What is the difference between `String` and `&str`?

### Answer
`String` is an owned, heap-allocated, growable type, while `&str` is a borrowed, fixed view into existing UTF-8 data. `String` is responsible for its memory and frees it on drop; `&str` owns nothing and simply refers to bytes owned elsewhere. You convert from `&str` to `String` with `.to_string()` or `String::from(...)`, and from `String` to `&str` cheaply by borrowing (`&my_string` coerces to `&str`). The practical guideline is to store and build text as `String` when you need ownership and mutation, but accept `&str` in function parameters so callers can pass either a literal or a borrowed `String`.

## Question 028

### Question
What is a slice?

### Answer
A slice is a borrowed view into a contiguous sequence of elements without owning them, written as `&[T]` for a general slice or `&str` for a string slice. For example, given `let v = vec![1, 2, 3, 4]`, the expression `&v[1..3]` is a slice referencing elements 1 and 2. A slice stores a pointer to the start and a length, so it is a lightweight, cheap reference to part of an array, vector, or string. Slices let functions operate on a range of data without copying it and without caring whether the underlying storage is an array or a `Vec`.

## Question 029

### Question
What is type inference in Rust?

### Answer
Type inference is the compiler's ability to determine the type of a binding from context, so you often do not need to write it explicitly. In `let x = 5;` the compiler infers `i32`, and in `let v: Vec<_> = items.collect();` it can infer the element type. Rust uses a powerful inference system (Hindley-Milner based) that looks at how a value is used throughout the function, not just its initializer. However, inference is local to a function: function signatures always require explicit types, which both documents the interface and keeps inference tractable. When the compiler cannot infer a type, it asks you to annotate.

## Question 030

### Question
When do you need explicit type annotations?

### Answer
You need annotations whenever the compiler cannot determine a unique type from context. The most common cases are function signatures (always required), constants and statics, and situations where a method like `parse` or `collect` could produce several possible types — for instance `let n: u32 = "42".parse().unwrap();` or using the turbofish `"42".parse::<u32>()`. You also annotate when you want to constrain a numeric literal to a specific type. Outside these cases, idiomatic Rust leans on inference and avoids redundant annotations, since the type is usually clear from the right-hand side.

## Question 031

### Question
What is the difference between statements and expressions?

### Answer
A statement performs an action and returns no value, while an expression evaluates to a value. In Rust, `let y = 6;` is a statement, and `6`, `5 + 1`, a function call, or a block `{ ... }` are expressions. This distinction matters because Rust is expression-oriented: a block's value is the value of its last expression if that expression has no trailing semicolon. That is why `let y = { let x = 3; x + 1 };` sets `y` to 4. Adding a semicolon turns an expression into a statement that discards the value, which is a frequent source of beginner confusion when a function unexpectedly returns `()`.

## Question 032

### Question
Is `if` an expression in Rust?

### Answer
Yes. `if`/`else` is an expression that evaluates to a value, so you can write `let number = if condition { 5 } else { 6 };`. Because it is an expression, both branches must produce values of the same type, otherwise the compiler cannot decide the type of the whole expression and reports an error. This is different from languages where `if` is only a statement and you need a separate ternary operator — Rust does not have a ternary operator because `if` already serves that role. The condition itself must be a `bool`; Rust will not implicitly convert other types to a boolean.

## Question 033

### Question
What is the `loop` keyword?

### Answer
`loop` creates an infinite loop that runs until you explicitly `break` out of it. Unlike `while true`, `loop` is special because `break` can return a value, making it an expression: `let result = loop { counter += 1; if counter == 10 { break counter * 2; } };`. This is the idiomatic way to retry an operation until it succeeds and capture the result. You can also label loops (`'outer: loop { ... }`) and `break 'outer;` to break out of an outer loop from within a nested one, which avoids awkward flag variables.

## Question 034

### Question
What is the `while` loop?

### Answer
A `while` loop repeats a block as long as a boolean condition remains true, checking the condition before each iteration: `while n != 0 { n -= 1; }`. It is the right choice when the number of iterations depends on a condition that changes during the loop and is not simply iterating over a collection. For iterating over collections, a `for` loop is preferred because it is clearer and avoids off-by-one and out-of-bounds mistakes. There is also `while let`, which loops as long as a pattern keeps matching, useful for draining values out of something like a stack.

## Question 035

### Question
What is the `for` loop?

### Answer
A `for` loop iterates over the items produced by an iterator, for example `for element in collection { ... }` or `for i in 0..5 { ... }` using a range. It is the most common and safest loop in Rust because it cannot go out of bounds and it expresses intent clearly. Under the hood, `for` works with anything implementing `IntoIterator`, so it can iterate over arrays, vectors, ranges, hash maps, and custom types. To get indices alongside values you commonly use `.enumerate()`, and to iterate without consuming the collection you iterate over a reference (`for x in &v`).

## Question 036

### Question
How do `break` and `continue` work, and what are loop labels?

### Answer
`break` exits the nearest enclosing loop immediately, and `continue` skips the rest of the current iteration and moves to the next. In a `loop`, `break value;` can also return a value from the loop expression. Loop labels, written with a leading apostrophe like `'outer:`, let you name a loop so that `break 'outer;` or `continue 'outer;` targets a specific enclosing loop rather than the innermost one. This is the clean way to handle nested loops where an inner condition should terminate or skip an outer loop, replacing the flag-and-check pattern common in other languages.

## Question 037

### Question
What is `match`?

### Answer
`match` is Rust's powerful pattern-matching control-flow construct that compares a value against a series of patterns and runs the code for the first one that matches. It is an expression, so it returns a value, and it is exhaustive: the compiler requires that every possible case is handled, which prevents forgetting a variant. A simple example is matching on an enum: `match coin { Coin::Penny => 1, Coin::Nickel => 5, ... }`. Patterns can bind parts of the value, match ranges, include guards with `if`, and use `_` as a catch-all, making `match` far more capable than a typical switch statement.

## Question 038

### Question
What is pattern matching?

### Answer
Pattern matching is comparing a value against the structure of patterns to both test its shape and destructure it into its parts in one step. Patterns appear in `match` arms, `if let`, `while let`, `let` bindings, and function parameters. For example, `let (x, y) = point;` destructures a tuple, and `match opt { Some(n) => n, None => 0 }` both checks which `Option` variant you have and extracts the inner value. This combination of testing and binding makes Rust code concise and is central to working with enums like `Option` and `Result`.

## Question 039

### Question
Why does `match` have to be exhaustive?

### Answer
`match` must cover every possible value of the matched type so the compiler can guarantee no case is silently unhandled, which eliminates a common source of bugs. If you add a new variant to an enum later, every non-exhaustive match becomes a compile error, forcing you to consider the new case — a powerful safety net during refactoring. When you genuinely want to handle the remaining cases together, you add a `_ => ...` catch-all arm or bind the rest to a name. Exhaustiveness is one of the reasons Rust's enums and `match` are so reliable in practice.

## Question 040

### Question
What does the `_` pattern do?

### Answer
The underscore `_` is a wildcard pattern that matches any value without binding it to a name. In a `match`, `_ => ...` serves as the catch-all arm for every case you have not explicitly handled, which is how you satisfy exhaustiveness when you do not care about the remaining values. You can also use `_` to ignore a parameter or part of a destructured value, like `let (_, y) = point;` to take only the second element. Using `_x` (underscore prefix on a name) is different: it binds the value but silences the unused-variable warning, whereas a bare `_` does not bind at all.

## Question 041

### Question
What is `if let`?

### Answer
`if let` is a concise way to match one specific pattern and ignore the rest, useful when a full `match` would be verbose. For example, `if let Some(x) = optional { println!("{x}"); }` runs the block only when `optional` is `Some`, binding the inner value to `x`, and you can add an `else` for the other cases. It trades exhaustiveness checking for brevity, so it is ideal when you care about exactly one variant. It is essentially syntactic sugar for a `match` with one meaningful arm and a `_ => ()` arm, and it reads more naturally when that is your intent.

## Question 042

### Question
What is `while let`?

### Answer
`while let` repeatedly runs a loop body as long as a pattern continues to match, which is perfect for consuming values until a collection is empty or an operation stops yielding results. A classic example is draining a stack: `while let Some(top) = stack.pop() { println!("{top}"); }` loops until `pop` returns `None`. It combines the looping of `while` with the pattern binding of `if let`, so on each iteration it both tests the condition and extracts the inner value. It keeps such loops concise and avoids manual checks and indexing.

## Question 043

### Question
What is ownership in Rust?

### Answer
Ownership is Rust's central memory-management discipline: every value has exactly one owning variable, and when that owner goes out of scope the value is automatically dropped (freed). This model lets Rust guarantee memory safety without a garbage collector, because the compiler always knows statically when each value's memory should be released. Ownership can be transferred (moved) to another variable or function, and it can be temporarily lent out through borrowing. Mastering ownership is the key to Rust, since most of the borrow checker's rules and many of its error messages stem directly from it.

## Question 044

### Question
What are the three rules of ownership?

### Answer
The three rules are: first, each value in Rust has an owner; second, there can be only one owner at a time; and third, when the owner goes out of scope, the value is dropped. These rules together ensure that memory is freed exactly once, at a well-defined moment, with no leaks and no double frees. The single-owner rule is what makes moves necessary: assigning an owned value to a new variable transfers ownership rather than making two owners. Understanding these three rules explains nearly all of Rust's move semantics and scope-based cleanup.

## Question 045

### Question
What is a move in Rust?

### Answer
A move is the transfer of ownership of a value from one variable to another. When you write `let s2 = s1;` for a heap-owning type like `String`, ownership of the data moves to `s2`, and `s1` is no longer valid — using it afterward is a compile error. Rust does this instead of a deep copy to keep assignment cheap (it copies only the pointer/length/capacity, not the heap data) while still guaranteeing a single owner so the memory is freed exactly once. Moves also happen when you pass a value to a function by value or return it. If you need both variables to remain usable, you either borrow or explicitly `clone`.

## Question 046

### Question
What happens when you assign one variable holding heap data to another?

### Answer
For a type that owns heap data, such as `String` or `Vec`, the assignment moves ownership rather than copying the underlying data. The new variable takes over the pointer, length, and capacity, and the old variable is invalidated so it cannot be used. This prevents a "double free" that would otherwise occur if both variables tried to free the same heap memory when they went out of scope. For simple stack-only types that implement `Copy` (like integers), the same syntax instead makes an independent copy and both variables stay valid, because there is no heap allocation to worry about.

## Question 047

### Question
What is the `Copy` trait?

### Answer
`Copy` is a marker trait for types whose values can be duplicated by a simple bitwise copy, so assigning or passing them does not move ownership but makes an independent copy and leaves the original usable. All the simple scalar types — integers, floats, `bool`, `char` — are `Copy`, as are tuples and arrays composed entirely of `Copy` types. A type can be `Copy` only if it does not own heap resources, which is why `String` and `Vec` are not `Copy`. You can derive `Copy` for your own simple structs (`#[derive(Copy, Clone)]`), but only when every field is itself `Copy`.

## Question 048

### Question
What is the `Clone` trait?

### Answer
`Clone` provides an explicit `.clone()` method that produces a deep, independent copy of a value, including duplicating any heap data it owns. Unlike `Copy`, cloning is explicit because it can be expensive, so Rust makes you opt in by calling `.clone()` rather than copying silently. For example, `let s2 = s1.clone();` gives you two independent `String`s, each owning its own heap buffer. You typically derive it with `#[derive(Clone)]`. Reaching for `.clone()` is a legitimate way to satisfy the borrow checker when you genuinely need a separate copy, but overusing it to "make errors go away" can hide unnecessary allocations.

## Question 049

### Question
What is the difference between `Clone` and `Copy`?

### Answer
`Copy` is an implicit, cheap, bitwise duplication that happens automatically on assignment for stack-only types, while `Clone` is an explicit, potentially expensive deep copy you invoke with `.clone()`. Every `Copy` type is also `Clone` (because a bitwise copy is a valid clone), but not every `Clone` type is `Copy` — `String` is `Clone` but not `Copy` because duplicating it requires allocating new heap memory. The practical distinction is cost and intent: `Copy` types are trivial to duplicate and you never notice it, whereas `Clone` signals that real work (often allocation) is happening and is therefore made visible in the code.

## Question 050

### Question
What is borrowing?

### Answer
Borrowing is accessing a value through a reference without taking ownership of it, so the original owner keeps the value and the borrow ends when the reference goes out of scope. You create a reference with `&` for shared (read-only) access or `&mut` for exclusive (mutable) access. Borrowing is how you let functions and other code use a value without the cost and ownership transfer of a move, for example passing `&my_string` so a function can read it and the caller still owns it afterward. The borrow checker enforces rules on borrows to guarantee that references are always valid and never cause data races.

## Question 051

### Question
What is a reference?

### Answer
A reference is a non-owning pointer to a value, written `&value`, that lets you read or (with `&mut`) modify the value without taking ownership of it. References are guaranteed by the compiler to always point to valid data — there are no null or dangling references in safe Rust. They are used pervasively to pass data to functions cheaply: `fn len(s: &String) -> usize` borrows the string so the caller retains ownership. You access the pointed-to value either implicitly (method calls auto-dereference) or explicitly with the `*` operator. A reference's validity is tracked by its lifetime, which the compiler usually infers.

## Question 052

### Question
What is the difference between a shared reference and a mutable reference?

### Answer
A shared reference `&T` grants read-only access and can be held by many borrowers at once, while a mutable reference `&mut T` grants exclusive read-write access and excludes all other references for its duration. This reflects the core borrowing rule: you may have either any number of shared references or exactly one mutable reference, but not both simultaneously. Shared references let multiple parts of the code observe the same data safely because none of them can change it; a mutable reference lets one part change the data while guaranteeing no one else is looking. This is how Rust prevents data races at compile time.

## Question 053

### Question
What are the borrowing rules?

### Answer
At any given time you may have either one mutable reference or any number of immutable references to a particular value, but never both at once, and references must never outlive the data they point to. The first rule — often summarized as "aliasing XOR mutability" — prevents data races and the subtle bugs that arise when data changes while something else is reading it. The second rule prevents dangling references. These rules are enforced by the borrow checker at compile time, so violations are caught before the program runs rather than causing undefined behavior, which is the heart of Rust's safety guarantees.

## Question 054

### Question
What is the borrow checker?

### Answer
The borrow checker is the part of the Rust compiler that statically verifies all references obey the borrowing rules: that no reference outlives its data and that you never have a mutable reference alongside other references. It tracks the lifetime (the span of valid use) of every borrow and rejects code that could lead to dangling pointers or data races. Modern Rust uses "non-lexical lifetimes," meaning a borrow is considered to end after its last actual use rather than at the end of the enclosing block, which makes many natural patterns compile. Wrestling with the borrow checker is the main learning curve in Rust, but it is what lets the language be both safe and fast.

## Question 055

### Question
How does Rust prevent dangling references?

### Answer
Rust prevents dangling references by ensuring, at compile time, that any reference cannot outlive the data it points to. If you tried to return a reference to a local variable from a function — `fn dangle() -> &String { let s = String::from("x"); &s }` — the compiler rejects it, because `s` is dropped when the function returns, which would leave the reference pointing at freed memory. The fix is usually to return the owned value instead, transferring ownership to the caller. This compile-time guarantee is why safe Rust has no use-after-free bugs, a frequent and dangerous error class in C and C++.

## Question 056

### Question
Why use string slices instead of indices when working with strings?

### Answer
String slices (`&str`) tie a portion of a string to the string's data so the compiler can guarantee the slice stays valid, whereas raw start/end indices are just numbers that can silently become wrong if the string changes. For instance, a function that finds the first word can return a `&str` slice of the input; if the original string were then cleared, the borrow checker would prevent using that slice, catching a bug that index-based code would miss. Slices also avoid copying, since they only borrow. This is a concrete example of how Rust's borrowing turns a potential runtime bug into a compile-time error.

## Question 057

### Question
Why can't you index a `String` by an integer to get a character?

### Answer
Because `String` is UTF-8 encoded, a single character may occupy one to four bytes, so a byte index does not correspond to a character position, and allowing `s[0]` would be ambiguous and potentially return half of a character. To avoid silently incorrect or invalid results, Rust simply does not implement integer indexing on strings. Instead you iterate with `.chars()` to get characters or `.bytes()` to get raw bytes, or take a byte-range slice like `&s[0..4]` (which panics if it would split a character boundary). This design forces you to be explicit about whether you mean bytes or characters, preventing a common class of internationalization bugs.

## Question 058

### Question
What is dereferencing with the `*` operator?

### Answer
The dereference operator `*` follows a reference to access or modify the value it points to. Given `let r = &mut x;`, you write `*r += 1;` to change the value `x` through the reference. Rust often inserts dereferences automatically — for example, method calls and field accesses auto-dereference, so you rarely write `*` explicitly when calling methods. You most often need it when doing arithmetic or assignment through a reference, or when comparing the pointed-to values. The same operator also works for smart pointers like `Box<T>` through the `Deref` trait, which is what makes them feel like ordinary references.

## Question 059

### Question
What happens to ownership when you pass a value to a function?

### Answer
Passing a value by value moves ownership into the function, just like assigning it to a new variable. For a `String`, after `takes_ownership(s);` the variable `s` is no longer usable in the caller, because the function now owns the value and will drop it when it returns (unless it gives ownership back). For `Copy` types like integers, a copy is passed instead and the original remains usable. To let a function use a value without taking ownership, you pass a reference (`&s` or `&mut s`) so the caller keeps the value after the call. Choosing between moving and borrowing in function signatures is a core design decision in Rust.

## Question 060

### Question
How do you return ownership from a function?

### Answer
A function returns ownership simply by returning a value by value; the returned value's ownership transfers to the caller. For example, `fn make() -> String { String::from("hi") }` creates a `String` and hands ownership to whoever calls it. A function can also take ownership and give it back, returning a value it received, which was a common pattern before borrowing made it largely unnecessary. Returning owned values is how you produce new data, while returning references is how you give borrowed views — but a returned reference must point to data that outlives the function, so you usually return owned values for freshly created data.

## Question 061

### Question
Why borrow a value in a function instead of moving it?

### Answer
You borrow when the function only needs to read or temporarily modify the value and the caller should keep using it afterward. Moving would transfer ownership into the function and invalidate the caller's variable, forcing the function to return the value just so the caller can keep it — awkward and verbose. Borrowing with `&` (or `&mut` for modification) avoids that: the caller lends the value, the function uses it, and ownership stays with the caller. This is why idiomatic Rust function signatures usually take `&str` or `&[T]` for read-only access, reserving by-value parameters for cases where the function genuinely needs to consume or store the value.

## Question 062

### Question
What is a struct?

### Answer
A struct is a custom data type that groups related named fields together, like `struct User { name: String, age: u32, active: bool }`. You create an instance with `User { name: ..., age: ..., active: ... }` and access fields with dot notation. Structs are how you model the concepts in your program as concrete types, and they pair with `impl` blocks that define their methods. Rust has three struct flavors: classic structs with named fields, tuple structs with positional fields, and unit structs with no fields. Structs are central to idiomatic Rust because they let you attach behavior and invariants to your data.

## Question 063

### Question
What is a tuple struct?

### Answer
A tuple struct is a struct whose fields are unnamed and accessed by position, defined like `struct Point(i32, i32);` and accessed with `.0` and `.1`. It is useful when you want a distinct named type but the fields do not need descriptive names, such as `struct Rgb(u8, u8, u8)`. A common use is the "newtype" pattern, wrapping a single value in a one-field tuple struct like `struct Meters(f64)` to give it a unique type, which prevents mixing it up with other `f64` values and lets you attach methods. Tuple structs combine the convenience of tuples with the type safety of named types.

## Question 064

### Question
What is a unit struct?

### Answer
A unit struct is a struct with no fields at all, declared like `struct Marker;`. Because it holds no data, it occupies no space, and it is mainly used as a type to attach behavior or to implement a trait when you do not need any state. For example, you might define a unit struct to represent a particular strategy or to serve as a typed token that implements some trait. It resembles the unit type `()` but is a distinct named type you can implement methods and traits on, which is its whole purpose.

## Question 065

### Question
What is an `impl` block?

### Answer
An `impl` block is where you define the methods and associated functions for a type, separating behavior from the data declaration. For a struct `Rectangle`, you write `impl Rectangle { fn area(&self) -> u32 { self.width * self.height } }`. You can have multiple `impl` blocks for the same type, and you also use `impl` to implement traits for a type (`impl Display for Rectangle`). This separation keeps the struct definition focused on its data while the `impl` block holds its operations, and it is how Rust achieves object-like behavior without classes.

## Question 066

### Question
What is the difference between `self`, `&self`, and `&mut self`?

### Answer
These are the three forms of the receiver in a method. `&self` borrows the instance immutably, so the method can read fields but not change them, and the caller keeps ownership — this is the most common form. `&mut self` borrows the instance mutably, allowing the method to modify the instance's fields. `self` (by value) takes ownership of the instance, consuming it, which is used for methods that transform or destroy the value, such as converting it into another type. Choosing the right receiver communicates the method's intent: read-only, mutating, or consuming.

## Question 067

### Question
What are associated functions and how do constructors work?

### Answer
An associated function is a function defined in an `impl` block that does not take `self`, so it is called on the type itself rather than on an instance, using `Type::function()`. The most common example is a constructor named `new`, like `Rectangle::new(width, height)`, which builds and returns an instance. Rust has no special constructor syntax; `new` is just a convention, and a type can have several differently named "constructor" functions (`from_str`, `with_capacity`, etc.). Associated functions are also how you get factory-like behavior and namespaced helpers tied to a type.

## Question 068

### Question
What is an enum?

### Answer
An enum is a type that can be exactly one of several named variants, defined like `enum Direction { North, South, East, West }`. Enums shine in Rust because each variant can also carry data of different types, making them "sum types" that model a value which is one of several distinct shapes. They pair naturally with `match`, which forces you to handle every variant. Enums are how Rust represents concepts like "this is either a success or an error" (`Result`) or "this is either something or nothing" (`Option`), and they are far more powerful than the integer-based enums of many other languages.

## Question 069

### Question
Can enum variants hold data?

### Answer
Yes, and this is one of Rust's most powerful features. Each variant can hold different kinds and amounts of data: `enum Message { Quit, Move { x: i32, y: i32 }, Write(String), ChangeColor(i32, i32, i32) }` has a unit variant, a struct-like variant, a single-value variant, and a tuple variant. When you `match` on such an enum, each arm can destructure and bind the data inside that variant. This lets a single type represent a whole family of related cases with their associated data, which is exactly how `Option<T>` carries a value in `Some(T)` and `Result<T, E>` carries either `Ok(T)` or `Err(E)`.

## Question 070

### Question
What is `Option`?

### Answer
`Option<T>` is a standard-library enum representing a value that may or may not be present, with two variants: `Some(T)` holding a value, and `None` representing absence. It is Rust's replacement for null: because the absence of a value is encoded in the type, the compiler forces you to handle the `None` case before you can use the inner value, eliminating null-pointer errors. You typically handle it with `match`, `if let`, or methods like `unwrap_or`, `map`, and `?`. Any function that might not return a result uses `Option`, such as `HashMap::get` returning `Option<&V>`.

## Question 071

### Question
Why does Rust use `Option` instead of null?

### Answer
Null references are a notorious source of crashes because the type system does not distinguish "a value" from "no value," so you can accidentally use a missing value and the program blows up at runtime. Rust avoids this by not having null at all and instead making absence explicit in the type with `Option<T>`. Since a value of type `T` is guaranteed to be present and a possibly-missing value must be `Option<T>`, the compiler requires you to explicitly handle the `None` case before accessing the inner value. This converts what would be a runtime null-pointer exception into a compile-time obligation, which is a major reliability win.

## Question 072

### Question
What is `Result`?

### Answer
`Result<T, E>` is the standard enum for operations that can fail, with two variants: `Ok(T)` containing a success value, and `Err(E)` containing an error. It is how Rust does recoverable error handling without exceptions: a function that might fail returns a `Result`, and the caller must decide how to handle both outcomes. You can `match` on it, use methods like `unwrap`, `expect`, `unwrap_or_else`, and `map_err`, or propagate the error upward with the `?` operator. Because errors are ordinary return values encoded in the type, the compiler ensures you cannot ignore the possibility of failure by accident.

## Question 073

### Question
What do `unwrap` and `expect` do?

### Answer
`unwrap` extracts the inner value from a `Some` or `Ok`, but panics (crashes the program) if the value is `None` or `Err`. `expect` does the same but lets you supply a custom panic message, like `file.expect("config file should exist")`, which makes failures far easier to diagnose. Both are convenient for quick prototypes, examples, and cases where failure truly indicates a bug, but using them in production code on values that can legitimately be absent or erroneous is risky because it turns a recoverable situation into a crash. Idiomatic code prefers `match`, `?`, or the `unwrap_or*` family for situations that should be handled gracefully.

## Question 074

### Question
What does the `?` operator do?

### Answer
The `?` operator is shorthand for propagating errors: applied to a `Result`, it returns the `Ok` value if successful, or returns early from the function with the `Err` if not. So `let f = File::open("x")?;` either gives you the file or causes the enclosing function to return the error to its caller. It works in functions whose return type is a compatible `Result` (or `Option`), and it dramatically reduces boilerplate compared to writing a `match` after every fallible call. It also performs automatic error conversion via the `From` trait, so a lower-level error can be turned into your function's error type as it propagates.

## Question 075

### Question
What is `panic!`?

### Answer
`panic!` is the macro that triggers an unrecoverable error, immediately starting to unwind the stack (running destructors) and terminating the current thread, usually printing a message and backtrace. It represents a bug or a situation the program cannot sensibly continue from, such as an index out of bounds or a failed `unwrap`. Panicking is appropriate for programmer errors and unrecoverable states, but not for ordinary expected failures like a missing file or invalid user input, which should return a `Result` instead. You can configure panics to abort immediately rather than unwind, which produces smaller binaries at the cost of not running destructors.

## Question 076

### Question
What is the difference between recoverable and unrecoverable errors?

### Answer
Recoverable errors are expected failure conditions that the program can reasonably respond to — a file not found, a network timeout, invalid input — and Rust represents them with `Result<T, E>`, leaving the caller to decide how to react. Unrecoverable errors are bugs or impossible states that indicate the program should not continue, and these use `panic!`. The distinction guides API design: return `Result` when failure is a normal, anticipated outcome the caller should handle, and panic only when continuing would be incorrect or when the error reflects a violated invariant. Mixing these up — panicking on routine failures — produces fragile programs that crash on ordinary conditions.

## Question 077

### Question
What is a trait?

### Answer
A trait defines shared behavior that types can implement, similar to an interface in other languages. You declare it with `trait Summary { fn summarize(&self) -> String; }` and implement it for a type with `impl Summary for Article { ... }`. Traits let you write code that works with any type implementing a given trait, which is the basis of Rust's generics and polymorphism. The standard library is full of traits like `Display`, `Clone`, `Iterator`, and `PartialEq`, and implementing them lets your types plug into language features and standard functions. Traits can also provide default method implementations that implementers may override.

## Question 078

### Question
Can traits have default method implementations?

### Answer
Yes. A trait can provide a default body for a method, which implementing types use automatically unless they override it. For example, a `Summary` trait might define `fn summarize(&self) -> String { String::from("(Read more...)") }`, so any type implementing `Summary` gets that behavior for free. Default methods can even call other (possibly required) methods of the same trait, letting you define a small set of required methods and build richer default behavior on top of them. This reduces boilerplate and lets a trait offer a useful baseline while still allowing customization where needed.

## Question 079

### Question
What is the `derive` attribute?

### Answer
`#[derive(...)]` is an attribute placed above a struct or enum that tells the compiler to automatically generate standard trait implementations for it, saving you from writing them by hand. For example, `#[derive(Debug, Clone, PartialEq)]` generates a debug-printing implementation, a deep-clone implementation, and an equality comparison. Derivable traits include `Debug`, `Clone`, `Copy`, `PartialEq`, `Eq`, `Hash`, `PartialOrd`, `Ord`, and `Default`. Deriving works only when every field also implements the trait, and the generated logic is the obvious field-by-field behavior; when you need custom behavior you implement the trait manually instead.

## Question 080

### Question
What are some commonly derived traits and what do they do?

### Answer
The most common are `Debug`, which enables printing a value with `{:?}` for debugging; `Clone`, which provides explicit deep copying via `.clone()`; `Copy`, which makes simple types duplicate implicitly on assignment; and `PartialEq`/`Eq`, which enable `==` comparisons. Also frequent are `Hash` (to use a type as a `HashMap` key), `PartialOrd`/`Ord` (to compare and sort), and `Default` (to produce a default instance with `Type::default()`). Deriving these is the idiomatic, low-effort way to make your types behave like built-in ones, and you derive only the traits your type actually needs.

## Question 081

### Question
What is the difference between `Display` and `Debug`?

### Answer
`Debug` is for developer-facing output, used with the `{:?}` format specifier, and can be derived automatically; it shows a value's structure for debugging and logging. `Display` is for user-facing output, used with `{}`, and must always be implemented by hand because how a value should be presented to a human is a design decision the compiler cannot guess. For example, a `Point` might derive `Debug` to print `Point { x: 1, y: 2 }`, but you would implement `Display` to print `(1, 2)`. Many types implement both; you reach for `Debug` while developing and `Display` for final output.

## Question 082

### Question
What are the `println!` and `format!` macros?

### Answer
`println!` prints formatted text to standard output with a trailing newline, and `format!` builds a `String` from the same formatting syntax instead of printing it. Both use a format string with `{}` placeholders that interpolate arguments, support named and positional arguments, and accept format specifiers like `{:?}` for debug output, `{:.2}` for two decimal places, and width/alignment options. Since recent Rust editions you can also inline variables directly: `println!("{name}")`. They are macros (note the `!`) rather than functions because they check the format string against the arguments at compile time, catching mismatches before the program runs.

## Question 083

### Question
What are generics in Rust?

### Answer
Generics let you write code that works over many types instead of duplicating it for each one, using type parameters written in angle brackets like `<T>`. A function `fn largest<T>(list: &[T]) -> &T` works for any slice element type that meets the required bounds. Generics are resolved at compile time through monomorphization, meaning the compiler generates a specialized concrete version for each type actually used, so there is no runtime cost compared to writing the code by hand. Generics underpin the standard collections (`Vec<T>`, `HashMap<K, V>`) and combine with traits to express "any type that can do X."

## Question 084

### Question
How do you write a generic function?

### Answer
You introduce one or more type parameters after the function name and use them in the signature, for example `fn first<T>(items: &[T]) -> &T { &items[0] }`. Often you must add trait bounds to specify what the type must be able to do, such as `fn largest<T: PartialOrd>(list: &[T]) -> &T`, which requires that values of `T` can be compared. Without such a bound, the compiler will reject operations it cannot guarantee the type supports. Generic functions let one definition serve many types while the compiler still checks every use, giving both flexibility and type safety.

## Question 085

### Question
How do you write a generic struct?

### Answer
You declare type parameters after the struct name and use them as field types, like `struct Point<T> { x: T, y: T }`, which can then hold two `i32`s or two `f64`s. You can use multiple parameters, `struct Pair<T, U> { first: T, second: U }`, when fields may differ in type. Methods on a generic struct are defined with `impl<T> Point<T> { ... }`, and you can also write `impl` blocks restricted to specific concrete types or specific bounds, so some methods exist only for certain `T`. Generic structs are how the standard collections are defined and how you build reusable data structures of your own.

## Question 086

### Question
What are trait bounds?

### Answer
A trait bound constrains a generic type parameter to only types that implement a given trait, so the generic code can rely on that behavior. You write them inline as `fn print_all<T: Display>(items: &[T])` or with a `where` clause for readability when there are several: `where T: Display + Clone`. The bound both enables you to call the trait's methods inside the function and tells callers what they must provide. Multiple bounds are combined with `+`. Trait bounds are the mechanism that makes generics safe and expressive — they let you say "this works for any type that can be displayed, compared, or cloned" rather than "any type at all."

## Question 087

### Question
What is `Vec`?

### Answer
`Vec<T>` is the standard growable array: a heap-allocated, contiguous, resizable list of values of the same type. You create one with `Vec::new()` or the `vec![1, 2, 3]` macro, add to it with `push`, and remove from the end with `pop`. It supports bounds-checked indexing (`v[0]`), iteration, and slicing, and it automatically grows its capacity as you add elements. `Vec` is the default collection in Rust when you need an ordered, dynamically sized sequence, and like all owning types it frees its heap memory when it goes out of scope. Indexing out of range panics, while `.get(i)` returns an `Option` for safe access.

## Question 088

### Question
What are common `Vec` operations?

### Answer
The core operations are `push(value)` to append, `pop()` to remove and return the last element as an `Option`, indexing with `v[i]` for direct (panicking) access, and `.get(i)` for safe access returning `Option<&T>`. You get the length with `.len()`, check emptiness with `.is_empty()`, and iterate with `for x in &v` (borrowing), `for x in &mut v` (mutating), or `for x in v` (consuming). Other frequent methods include `contains`, `insert`, `remove`, `iter().map(...).collect()`, and `sort`. A common beginner pitfall is trying to hold a reference into a `Vec` while also mutating it, which the borrow checker forbids because growing the `Vec` could move its data.

## Question 089

### Question
What is `HashMap`?

### Answer
`HashMap<K, V>` is the standard key-value collection that stores mappings from keys to values using hashing for fast lookup, insertion, and removal. You create one with `HashMap::new()`, insert with `map.insert(key, value)`, and look up with `map.get(&key)`, which returns an `Option<&V>` since the key may be absent. Keys must implement `Hash` and `Eq`. It lives in `std::collections`, so you typically `use std::collections::HashMap;`. The `entry` API (`map.entry(key).or_insert(0)`) is a powerful idiom for "get or create," commonly used for counting. Unlike `Vec`, a `HashMap` has no guaranteed iteration order.

## Question 090

### Question
What is an iterator?

### Answer
An iterator is a value that produces a sequence of items one at a time, defined by the `Iterator` trait whose key method is `next()`, returning `Some(item)` until the sequence is exhausted, then `None`. You usually obtain one by calling `.iter()` (yielding references), `.iter_mut()` (mutable references), or `.into_iter()` (owned values) on a collection. Iterators are central to idiomatic Rust because they let you express data transformations declaratively with adapter methods like `map` and `filter`, and they are zero-cost: the compiler optimizes iterator chains down to code as efficient as a hand-written loop. A `for` loop is really just consuming an iterator.

## Question 091

### Question
What do `map`, `filter`, and `collect` do?

### Answer
`map` transforms each item by applying a closure, producing a new iterator of the results, as in `numbers.iter().map(|x| x * 2)`. `filter` keeps only items for which a closure returns true, like `.filter(|x| **x > 0)`. `collect` consumes an iterator and gathers its items into a collection such as a `Vec`, `String`, or `HashMap`, often needing a type annotation to know what to build: `let v: Vec<i32> = it.collect();`. These adapters chain together to express pipelines like "double every number then keep the even ones" clearly and efficiently. `map` and `filter` are lazy and do nothing until a consumer like `collect` or a `for` loop drives them.

## Question 092

### Question
What does it mean that iterators are lazy?

### Answer
Iterator adapters like `map` and `filter` are lazy, meaning they do no work when called — they just build a description of the computation. Nothing actually happens until a consuming operation drives the iterator, such as `collect`, `sum`, `count`, `for`, or `for_each`. So `v.iter().map(|x| expensive(x));` on its own performs zero calls to `expensive`. Laziness lets you compose long chains without creating intermediate collections at each step and lets the compiler fuse the whole chain into one efficient pass. A common beginner mistake is writing a `map` for its side effects and being surprised it never runs because nothing consumed the iterator.

## Question 093

### Question
How does the `for` loop relate to iterators?

### Answer
A `for` loop is syntactic sugar for consuming an iterator: `for x in collection` calls `collection.into_iter()` to get an iterator and then repeatedly calls `next()` until it returns `None`. This is why you can `for` over anything implementing `IntoIterator`, including ranges, arrays, vectors, and hash maps. Whether you iterate by reference or by value depends on what you put after `in`: `for x in &v` borrows each element, `for x in &mut v` borrows mutably, and `for x in v` consumes the collection and moves each element out. Understanding this connection explains why the same collection behaves differently in a `for` loop depending on the `&`.

## Question 094

### Question
What is a closure?

### Answer
A closure is an anonymous function you can define inline and that can capture variables from the surrounding scope, written with vertical bars like `|x| x + 1` or `|a, b| { a + b }`. Closures are used heavily with iterators (`map`, `filter`), with methods like `unwrap_or_else`, and anywhere you want to pass behavior as a value. Unlike plain functions, they can refer to variables in their environment, which the compiler captures by reference or by value as needed. Their parameter and return types are usually inferred. Closures are the lightweight, expressive way to pass small bits of logic around in idiomatic Rust.

## Question 095

### Question
How do closures capture their environment?

### Answer
A closure captures the variables it uses from the enclosing scope in the least restrictive way that works: by immutable reference if it only reads them, by mutable reference if it modifies them, or by value (move) if it needs ownership. You can force capture by value with the `move` keyword, as in `move || println!("{data}")`, which is essential when a closure outlives the current scope — for instance when it is passed to a new thread. This automatic, minimal capture keeps closures convenient while still obeying the borrowing rules, so a closure that borrows its environment is subject to the same aliasing checks as any other borrow.

## Question 096

### Question
What is a lifetime?

### Answer
A lifetime is the compiler's name for the span during which a reference is valid, used to ensure references never outlive the data they point to. Most lifetimes are inferred and invisible, but sometimes you must write them explicitly with syntax like `fn longest<'a>(x: &'a str, y: &'a str) -> &'a str`, which tells the compiler the returned reference lives at least as long as both inputs. Lifetimes do not change how long values live; they are purely annotations that let the borrow checker verify relationships between references. They are one of Rust's harder beginner topics, but the key idea is simply: a reference must not be used after the thing it points to is gone.

## Question 097

### Question
Why do lifetimes exist?

### Answer
Lifetimes exist so the borrow checker can guarantee at compile time that every reference points to valid, still-living data, preventing dangling references without a garbage collector. When a function takes and returns references, the compiler sometimes cannot tell on its own how the output reference relates to the inputs, so lifetime annotations supply that information. They make explicit the otherwise-hidden constraint "this returned reference comes from one of these inputs and is valid as long as that input is." In the common cases Rust applies lifetime elision rules to infer them automatically, so beginners mostly encounter explicit lifetimes only when returning references derived from multiple parameters.

## Question 098

### Question
What is `Box<T>`?

### Answer
`Box<T>` is the simplest smart pointer: it stores a value on the heap and owns it, while the `Box` itself (a pointer) lives on the stack. You create one with `Box::new(value)`, and it frees the heap allocation automatically when it goes out of scope. You reach for `Box` in three main situations: to store a value whose size is not known at compile time (such as a recursive type like a tree or linked list), to move a large value without copying it, and to hold a trait object like `Box<dyn Trait>` for dynamic dispatch. Because it implements `Deref`, you use the boxed value almost exactly as if it were not boxed.

## Question 099

### Question
What is RAII and the `Drop` trait?

### Answer
RAII (Resource Acquisition Is Initialization) is the principle that a resource's lifetime is tied to a value's scope, so cleanup happens automatically when the value goes out of scope. Rust builds this in: when an owning value is dropped, its memory and any resources it holds are released deterministically. The `Drop` trait lets you customize that cleanup by implementing `fn drop(&mut self)`, which the compiler calls automatically at the end of the value's scope — useful for closing files, releasing locks, or freeing custom resources. You never call `drop` manually through the trait; if you need early cleanup you call the standalone `std::mem::drop(value)` function. This is how Rust guarantees no leaks without a garbage collector.

## Question 100

### Question
What should a Junior Rust developer be comfortable with?

### Answer
A Junior should be fluent in the ownership model — moves, borrowing, and the rule that you have either one mutable or many shared references — because most compiler errors trace back to it. They should comfortably use `Option` and `Result` with `match`, `if let`, and the `?` operator instead of reaching for `unwrap` everywhere, model data with structs and enums, and add behavior through `impl` blocks and basic traits. They should use the core collections (`Vec`, `String`, `HashMap`) and iterator adapters like `map`, `filter`, and `collect`. Above all, the right mindset is to treat borrow-checker errors as the compiler pointing out a real lifetime or aliasing issue to fix in the design, rather than an obstacle to work around with excessive cloning.

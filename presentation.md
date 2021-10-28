---
theme: gaia
marp: true
title: What can Rust teach us about writing secure code
paginate: true
---
# What can Rust teach us about writing secure code
![bg left](img/rust-language.jpg)

---
# Who am I
![h:4cm](img/csharp.png) ![h:4cm](img/android.png) ![h:4cm](img/python.png) ![h:4cm](img/scala.png)

![h:4cm](img/rust.png)

---
![bg right](img/car_charger.jpg)
![h:6cm](img/logo-dreamsolution.png)
![h:6cm](img/logo-tandemdrive.jpg)

---
# This talk
* Introduce Secure Programming?
* Show a little bit of Rust
* What 'Patterns' can we re-use in other languages?

---
# So what is secure programming?

---
![bg left](img/checklist.jpg)

# Checklists?
* Input Validation
* Output Encoding
* Authentication and Password Management
* Cryptographic Practices
* Error Handling and Logging
* etc.

--- 
![bg right](img/confused.jpg)
- Checklists are useful but;
  * Is your checklist complete?
  * Does this checklist fit my use-case?
  * Where did checklists items come from?

* What are we trying to do anyway?

---
# My take on secure programming:
* ### Programming 
  - Write code that can do the stuff you want
* ### Secure programming
  - Write code that cannot do anything else

* But this is hard;
  - The stuff you want your program to do is bounded
  - But the stuff you don't want is limitless
---
How do we make sure our code cannot do unexpected things

* Unit tests?
* Formal verification?
* Code by Contract (Eiffel)
  * Define preconditions, postconditions and invariants

---
# How can Rust help us here?
* A flexible, expressive type system
* No undefined behavior
* No null
* Ownership, borrow checker and lifetimes
  * This allows for compiled-in memory management
  * No data-races, no use-after free, memory safe
* 'Zero cost abstractions'
* (as long as you don't use the `unsafe` keyword)

---
# What does this look like?
```rust
fn main() {
    let who: String = "World".to_string();
    greet(who);
}

fn greet(who: String) {
    println!("Hello {}", who);
}
```

---

## The type system: Product types
Structs and enums
```rust
struct Customer {
    name: String,
    address: String,
    email: String,
}
```
```rust
let customer = ("Mendelt Siebenga", "Somewhere 42", "mendelt@mendelt.nl");
let (name, address, email) = customer;
```

---
## The type system: Sum types

```rust
enum Shape {
    Circle { radius: u32 },
    Square { width: u32, height: u32 },
    Text(String),
    Point,
}
```
This is called a discriminated union

---
## No null, but you can wrap things in an Option
```rust
enum Option<T> {
    Some(T),
    None,
}
```

```rust
fn greet(name: Option<String>) {
    match name {
        Some(name) => println!("Hello {}", name);
        None => println!("I don't know who you are but hello anyway");
    }
}
```
---
## No exceptions but a Result type
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```
```rust
fn read_line_from_file(filename: Path) -> Result<String, Error> {
    // ...
}

match read_line_from_file("filename.txt") {
    Ok(line) => println!("the file contained: {}", line);
    Err(error) => //... handle error here...
}
```

---
## Ownership, borrow checker and lifetimes
This will not compile
```rust
fn main() {
    let who: String = "World".to_string();
    greet(who);
    greet(who);  // You can't do this, `who` has already moved
}
```
When you 'move' a variable you cannot use it anymore

---
But you can borrow
```rust
greet(&who);  // A reference will 'borrow' the variable
```
- References borrow the variable
  - You can have as many references as you want but they can't outlive the original
  - And you can't use them to change the original

```rust
greet(&mut who);  // A reference will 'borrow' the variable
```
- Mutable references allow you to change the original
  - But you can have only one
---

# How does this make Rust 'secure'?
* Most common security issues are caught at compile time
  * No use-after-free
  * No data-races
  * No null-reference exceptions
* Lots of other bugs are caught too
  * Pre- and post-conditions can be encoded in types
* Rust allows you to express 'valid' behavior in types
  * The compiler can do (limited) static analysis

---
# 'Get the hangover before the party'

* It can take some time to make the compiler happy but you'll be surprised how few errors remain afterwards.
* Rust does most of its checks at compile-time. Most of those are optimized out for faster code at runtime.
* 
---

# Patterns
What security 'Patterns' can we extract that might be re-usable?

---
# 'Make invalid states unrepresentable'
* Use types to represent your domain
  * Do not 'stringly' type your application
  * Define your own 'primitive' types (postal code, ids etc)
  * Design your types to only allow valid values
* Type State
  * Have different types for the same value in different states
  * Methods that change the state return the value as a different type
---
* Wrap resources to limit allowed usage
  * We already saw the Option and Result type
  * A Rust Mutex also wraps the value it protects
* Token traits (interfaces)
  * Use empty interfaces to 'tag' your variables and types
  * `Send` and `Sync` in Rust

---
![bg right](img/mrmiyagi.jpg)
# "Be the borrow checker"
* Follow resources and their references through your code
* Where are references copied and mutated?
* Can references outlive values?

---
# What about dynamically typed languages?
Many of these patterns depend on having a static type-checker

* Use debug-assertions to check invariants, pre- and post-conditions in your code
* Write 'smoke-tests' to trigger these assertions

---
![bg left](img/meetup.jpeg)
# Next Meetup
We plan to organize more of these meetups in and around Delft

Follow Delft Developers on meetup.com

The next meetup is planned for the _**13th of januari**_

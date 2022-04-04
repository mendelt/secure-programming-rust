---
theme: gaia
marp: true
title: What can Rust teach us about writing secure code
paginate: true
---
## What can Rust teach us about writing secure code
![bg left](img/rust-language.jpg)

---
## Goals for this talk

* Introduce Rust
* Show what makes Rust a secure language
* Extract 'Patterns' that you might re-use in other languages

---
![bg left](img/checklist.jpg)

## What is secure programming?

* Checklists?
  - OWASP
  - SEI CERT
  - etc.

--- 
![bg right](img/confused.jpg)
- Checklists are useful but...
  * Does the checklist fit your use-case and stack?
  * How do you know you're not missing anything?
  * They seem kind of arbritrary

* Maybe we need some underlying principles

---
## My take on secure programming:

* ### Programming 
  - Write code that _can_ do the stuff you want
* ### Secure programming
  - Write code that _cannot_ do anything else

---
## How do you prevent unexpected behavior

* Unit tests?
* Formal verification?
* Static analysis?
* Code by Contract (Eiffel, Bertrand Meyer)
  * Define preconditions, postconditions and invariants
  * and then somehow check these

---
## How can your language help you?
* Lets look at Rust

---
## It looks familiar at first sight
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
## But then you run into ownership
This will not compile:
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
- Mutable references do allow you to change the original
  - But you can only have one
---

## The type system: Product types
Structs and tuples look familiar
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
Enums are a bit weird and can have payloads

```rust
enum Shape {
    Circle { radius: u32 },
    Square { width: u32, height: u32 },
    Text(String),
    Point,
}
```

---

## Add behavior to types with Traits
```rust
trait Drawable {
    fn draw(canvas: Canvas);
}

impl Drawable for Shape {
    fn draw(canvas: Canvas) {
        // ..
    }
}
```

---
## Generics allow for flexibility
```rust
trait Canvas {
    // ...
}

trait Drawable<C: Canvas> {
    fn draw(canvas: C);
}

impl Drawable for Shape {
    // ...
}

```

---

## How does this make Rust 'secure'?
---

## The ownership model gives memory safety
* No use-after-free
* No data-races
* Allows for "compile-time-garbage-collection"

---
## No null or exceptions but Option and Result wrappers
```rust
enum Option<T> {
    Some(T),
    None,
}

enum Result<T, E> {
    Ok(T),
    Err(E),
}
```
---
## These enforce null and error checks before you can access values

```rust
fn greet(name: Option<String>) {
    match name {
        Some(name) => println!("Hello {}", name);
        None => println!("I don't know who you are but hello anyway");
    }
}
```

---
## No null-pointer errors
- Variables are always initialized
- Null checks are enforced but only need to be done once
- No use of uninitialized memory
## No unhandled exceptions
- Just like null checks, error handling is enforced
- But only needs to be done once. The check 'unwraps' the value

---

## Patterns
What security 'Patterns' can we extract that might be re-usable?

---
## Make invalid states unrepresentable
* Design your types around invariants
* Check pre-conditions in constructors
* Check post-conditions when converting between types

* Use this for types in your domain
  - Do not 'Stringly' type your domain model but define different types
  - Postal code
  - Telephone number
  - IP address etc.
---
## Use Type-State
* Use different types for values in different states
* Only allow operations that are valid in that state
* Method that change the state return a different type
* Examples:
  * Option and Result types to enforce error handling and null checks
  * The Rust Mutex wraps the value it protects so you can only use it after taking a lock
  * Enforce initialization

---

## Wrapup
* Use types in your language to encode "contracts"
* Use the compiler to statically check those contracts
* Use assertions and smoke-tests if you don't have a compiler
* Use Rust if you want the extra safety of compile-time memory management

---
![bg](img/questions.jpeg)


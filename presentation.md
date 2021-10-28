---
theme: gaia
marp: true
title: What can Rust teach us about writing secure code
paginate: true
---
# What can Rust teach us about writing secure code
![bg left](img/rust-language.jpg)

---
# Who is this guy anyway?
A background in
![h:4cm](img/csharp.png) ![h:4cm](img/android.png) ![h:4cm](img/python.png) ![h:4cm](img/scala.png)

and
![h:4cm](img/rust.png)

---
![bg right](img/car_charger.jpg)

I work at
![h:6cm](img/logo-dreamsolution.png)

On a product called
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
  * Is the checklist complete?
  * Does the checklist fit your use-case?
  * Who came up with these checklists items?

* What are we trying to do anyway?

---
# My take on secure programming:
* ### Programming 
  - Write code that _can_ do the stuff you want
* ### Secure programming
  - Write code that _cannot_ do anything else

* But this is hard;
  - The stuff you want your program to do is bounded
  - But the stuff you don't want is limitless
---
How do we make sure our code cannot do unexpected things

* Unit tests?
* Formal verification?
* Code by Contract (Eiffel, Bertrand Meyer)
  * Define preconditions, postconditions and invariants
  * and then somehow check these

---
- But often this requires us to run external tools
* Or the checks are done at runtime and only trigger when you happen to hit an error

* I want to
  * Encode my pre-, post-conditions and invarients in my code
  * Have the compiler check them
---
# How can Rust help us here?
* No undefined behavior
* No null
* Ownership, borrow checker and lifetimes
  * This allows for compiled-in memory management
  * No data-races, no use-after free, memory safe
  * (as long as you don't use the `unsafe` keyword)
* A flexible, expressive type system

---
# And it looks quite familliar at first sight
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
Structs and tuples
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

## The type system: Traits
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
Rust is not object oriented but it does allow for abstractions and inheritance using Traits which are similar to interfaces in other languages.

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
remember the example hello world program?
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
  - But you there can be only one
---

# How does this make Rust 'secure'?
* Most common security issues are caught at compile time
  * No use-after-free
  * No data-races
  * No null-references
  * No unhandled errors
* Lots of other bugs are caught too
  * Pre-, post-conditions and invariants can be encoded in types
  * The compiler can do (limited) static analysis

---
# 'Get the hangover before the party'

* It can take some time to make the compiler happy but you'll be surprised how few errors remain afterwards.
* Rust does most of its checks at compile-time. Most of those are optimized out for fast code at runtime.

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
- Wrap resources to limit allowed usage
  * We already saw the Option and Result type
  * A Rust Mutex wraps the value it protects so you can only use it after taking a lock
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
Many of these patterns depend on having a compiler, but we can 'fake' that (a little bit)

* Use debug-assertions to check invariants, pre- and post-conditions
* Write 'smoke-tests' to trigger these assertions

---
![bg](img/questions.jpeg)

---
![bg left](img/meetup.jpeg)
# Next Meetup
We plan to organize more of these meetups in and around Delft

Follow Delft Developers on meetup.com

The next meetup is planned for the _**13th of januari**_

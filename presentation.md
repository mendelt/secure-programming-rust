---
theme: gaia
_class: lead
marp: true
title: What can Rust teach us about writing secure code

---
# What can Rust teach us about writing secure code
![bg left](img/rust-language.jpg)

---
# Who am I
* Programmer for 20+ years
* ![h:4cm](img/csharp.png) ![h:4cm](img/android.png) ![h:4cm](img/python.png) ![h:4cm](img/scala.png)
* ![h:4cm](img/rust.png)

---
* Dreamsolution
* Tandemdrive
---
* # Today
  * What is Secure Programming?
  * Show a little bit of Rust
  * Extract some of the 'patterns'
* # Goals
  * Give a deeper understanding on what secure software is
  * Give some recipes to write more secure code
  * Make you try my favorite language

---
# So what is secure programming?

---
![bg left](img/checklist.jpg)

# Checklists?
* Input Validation
* Output encoding
* Authentication and password management
* Cryptographic Practices
* Error handling and Logging
* etc.

--- 
![bg right](img/confused.jpg)
* Checklists are usefull but;
  * Why these?
  * Am I missing things?
  * Where do these come from?
  * What are we trying to achieve anyway?

---
# Secure programming, my definition:
* ### Programming: Writing code that can do the stuff you want
* ### Secure programming: Writing code that cannot do anything else
---
* This is a usefull definition
  * You can look at small pieces of code and reason them
  * And then extrapolate to larger pieces of code

* But this is hard;
  * The stuff you do want is bounded
  * But the amount of stuff you don't want is limitless
---
How do we make sure our code cannot do unexpected things

* Unit tests?
* Formal verification
* Code by Contract (Eiffel)
  * Define preconditions, postconditions and invariants

---
# An taste of Rust
```rust
fn main() {
    let who: String = "World".to_string();
    greet(who);
}

fn greet(who: String) {
    println!("Hello {}", who);
}
```
* No uninitialized variables
* No undefined behavior in the language
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
## The type system: Generics
```rust
let list_of_numbers: Vec<i32> = vec![56, -42, 16];
```

---
## No null, but we do have options
```rust
fn greet(name: Option<String>) {
    match name {
        Some(name) => println!("Hello {}", name);
        None => println!("I don't know who you are but hello anyway");
    }
}
```
---
## Error handling: Result type
```rust
fn read_line_from_file(filename: Path) -> Result<String, Error> {
    // ...
}

match read_line_from_file("filename.txt") {
    Ok(line) => println!("the file contained: {}}", line),
    Err(error) => //... handle error here...
}

```
---
```rust
enum Option<T> {
    Some(T),
    None,
}
```
```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```
---
## Ownership, borrow checker and lifetimes


---
# How does this make Rust 'secure'?
- Rust gives you the hangover before the party.

The expressive type-system allows you to specify wanted behavior in detail
The compiler checks 

# Patterns


* Make invalid states unrepresentable


* Type State
* Type state

Wrapping resources



---
You get the hangover before the party

It can take some time to make the compiler happy but you'll be surprised how few errors remain afterwards.

More compile-time guarantees means less runtime-checks. Which is better optimization and faster code

---
# What about other languages?
Define pre/post conditions
Figure out how to encode them in your types
Domain Driven Design

---
# Staticly typed languages

---
# Dynamically typed languages



--- Next meetup
We plan to 
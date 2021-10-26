---
theme: gaia
_class: lead
marp: true
paginate: true
backgroundColor: #fff
---

# What can Rust teach us about secure programming?

---
# Who am I

---
# What makes Rust so special?
- Rust is a language that was designed with secure programming in mind
- Rust is consistently voted the most loved language by its users

This is an interesting combination of properties

---
# What is secure programming
If you search online you'll find checklists like this;

- Input Validation
- Output encoding
- Authentication and password management
- Access control
- Cryptographic Practices
- Error handling and Logging
- Data Protection
etc.

--- 
# A more holistic approach
Programming is writing code that can do stuff
Secure programming is writing code that cannot do anything else

More emphasis on correct code

---
# Existing work
- Formal Proofs
- Static analysis
- Unit testing

---
# An intro to Rust

- Basic syntax
  - Very flexible type system
  - No OOP but Traits + inheritance (similar to Haskell Type classes)
- No undefined behavior
  - No uninitialized variables -> MaybeUnInit type
- No null -> Option type
- No unhandled errors -> Result type
- Ownership, borrow checker and lifetimes

---
# Result
Memory safe
Certain classes of errors are 'impossible' to make
- Use-after-free
- Null reference errors
- Data races
- Buffer overflow errors

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


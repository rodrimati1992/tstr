[![Rust](https://github.com/rodrimati1992/tstr_crates/workflows/Rust/badge.svg)](https://github.com/rodrimati1992/tstr_crates/actions)
[![crates-io](https://img.shields.io/crates/v/tstr.svg)](https://crates.io/crates/tstr)
[![api-docs](https://docs.rs/tstr/badge.svg)](https://docs.rs/tstr/*)

This crate provides an encoding of type-level strings as types.

# Examples

### Indexing

This example demonstrates how you can use type-level strings,
and the [`Index`] trait, to access fields of generic types by name.

```rust
use std::ops::Index;

use tstr::{TS, ts};

fn main(){
    takes_person(&Person::new("Bob".into(), "Marley".into()));

    takes_person(&OtherPerson::new("Bob", "Marley"));
}

fn takes_person<P>(pers: &P)
where
    P: Index<TS!(name), Output = str> + Index<TS!(surname), Output = str>
{
    assert_eq!(&pers[ts!(name)], "Bob");
    assert_eq!(&pers[ts!(surname)], "Marley");
}


use person::Person;
mod person {
    use std::ops::Index;

    use tstr::TS;
    
    pub struct Person {
        name: String,
        surname: String,
    }
    
    impl Person {
        pub fn new(name: String, surname: String) -> Self {
            Self{name, surname}
        }
    }
    
    impl Index<TS!(name)> for Person {
        type Output = str;
        
        fn index(&self, _: TS!(name)) -> &str {
            &self.name
        }
    }
   
    impl Index<TS!(surname)> for Person {
        type Output = str;
        
        fn index(&self, _: TS!(surname)) -> &str {
            &self.surname
        }
    }
}

use other_person::OtherPerson;
mod other_person {
    use std::ops::Index;

    use tstr::TS;
    
    pub struct OtherPerson {
        name: &'static str,
        surname: &'static str,
    }
    
    impl OtherPerson {
        pub fn new(name: &'static str, surname: &'static str) -> Self {
            Self{name, surname}
        }
    }
    
    impl Index<TS!(name)> for OtherPerson {
        type Output = str;
        
        fn index(&self, _: TS!(name)) -> &str {
            self.name
        }
    }
   
    impl Index<TS!(surname)> for OtherPerson {
        type Output = str;
        
        fn index(&self, _: TS!(surname)) -> &str {
            self.surname
        }
    }
}

```

# Macro expansion

This library reserves the right to change how it represent type-level strings internally
in every single release, and cargo feature combination.

This only affects you if you expand the code generated by macros from this crate,
and then use that expanded code instead of going through the macros.

# Cargo features

- `"use_syn"`:
Changes how literals passed to the macros of this crate are parsed to use the `syn` crate.
Use this if there is some literal that could not be 
parsed but is a valid str/integer literal.

- `"min_const_generics"`: 
changes the representation of type-level strings to use many `char` const parameter, 
making for better compiler errors for non-alphanumeric-ascii strings.
Requires Rust 1.51.0,
since this feature reaches the stable channel on March 25, 2021 as part of  that compiler version.

- `"const_generics"`: 
changes the representation of type-level strings to use a `&'static str` const parameter, 
making for better compiler errors, and a few more features.
Requires `&'static str` to be stably usable as const parameters.

- `"nightly_const_generics"`: Equivalent to the `"const_generics"` feature,
but enables the nightly compiler features to use `&'static str` const parameters.

- `"for_examples"`: Enables the `for_examples` module, 
with a few types used in documentation examples.

# No-std support

This crate is unconditionally `#![no_std]`, and can be used anywhere that Rust can be.

# Minimum Supported Rust Version

This crate supports Rust versions back to Rust 1.40.0.

[`Index`]: https://doc.rust-lang.org/std/ops/trait.Index.html

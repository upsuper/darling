# Darling

`darling` is a crate for proc macro authors, which enables parsing attributes into structs. It is heavily inspired by `serde` both in its internals and in its API.

# Usage
`darling` provides a set of traits which can be derived or manually implemented.

1. `FromMetaItem` is used to extract values from a meta-item in an attribute. Implementations are likely reusable for many libraries, much like `FromStr` or `serde::Deserialize`. Trait implementations are provided for primitives, some std types, and some `syn` types.
1. `FromDeriveInput` is implemented or derived by each proc-macro crate which depends on `darling`. This is the root for input parsing; it gets access to the identity, generics, and visibility of the target type, and can specify which attribute names should be parsed or forwarded from the input AST.
1. `FromField` is implemented or derived by each proc-macro crate which depends on `darling`. Structs deriving this trait will get access to the identity (if it exists), type, and visibility of the field.

# Example

```rust,ignore
#[macro_use]
extern crate darling;

#[derive(FromMetaItem)]
pub struct Lorem {
    #[darling(default = "local", rename = "sit")]
    ipsum: bool,
    #[darling(default)]
    dolor: Option<String>,
}
```

# Features
Darling's features are built to work well for real-world projects.

* **Defaults**: Supports struct- and field-level defaults, using the same path syntax as `serde`.
* **Field Renaming**: Fields can have different names in usage vs. the backing code.
* **Auto-populated fields**: Structs deriving `FromDeriveInput` and `FromField` can declare properties named `ident`, `vis`, `ty`, `attrs`, and `generics` to automatically get copies of the matching values from the input AST.
* **Mapping function**: Use `#[darling(map="path")]` to specify a function that runs on the result of parsing a meta-item field. This can change the return type, which enables you to parse to an intermediate form and convert that to the type you need in your struct.
* **Skip fields**: Use `#[darling(skip)]` to mark a field that shouldn't be read from attribute meta-items.


# To Do
* [ ] Finish error-handling story
* [ ] Add more type conversions
* [ ] Improve error diagnostics
# Learning Rust

Documenting my learning journey through [24 days from Node.js to Rust](https://vino.dev/blog/node-to-rust-day-1-rustup/)

## Day 1

- install rust via `rustup` https://rustup.rs/
- installed rustup completions via https://github.com/pkulev/zsh-rustup-completion
- learned about rust-toolchain.toml for specifying project deps

## Day 2

- learned about cargo
- init repo with `cargo init`
- installed `cargo-edit` to add `add`, `rm`, `upgrade`, and `set-version` commands
- installed `just` as a better version of make
- learned about workspace and `cargo-workspace` but did not install
- learned about macros and `cargo-expand` to reveal the compiled code
- learned about `tomlq` as a `jq` for toml files

## Day 3

- set up vscode extensions

- installed the [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=matklad.rust-analyzer) vscode extension
  - currently preferred over the official extension
  - learned extensions only highlight files connected to root via import

```json
"rust-analyzer.checkOnSave.command": "clippy"
```

- optional remove line hints with:

```json
{
  "rust-analyzer.inlayHints.enable": false,
  "rust-analyzer.inlayHints.chainingHints": false,
  "rust-analyzer.inlayHints.parameterHints": false
}
```

- optional remove auto download

```json
{
  "rust-analyzer.updates.askBeforeDownload": true
}
```

- installed [vscode-lldb](https://marketplace.visualstudio.com/items?itemName=vadimcn.vscode-lldb) for debugging
- installed [even-better-toml](https://marketplace.visualstudio.com/items?itemName=tamasfe.even-better-toml) for toml syntax highlighting
  - originally installed better-toml but it did not include formatting
- installed [crates](https://marketplace.visualstudio.com/items?itemName=serayuzgur.crates) for visual dependency updates
- installed [search-crates](https://marketplace.visualstudio.com/items?itemName=belfz.search-crates-io) for autocomplete while adding dependencies to toml

## Day 4

- learned about `cargo run` vs `cargo build [--release]`
- learned about `println!()` as a macro for printing to STDOUT
  - cant give string variable directly. requires string literal using template formatting syntax. ex:

```rust
println!("{}", greet)
```

- learned `&str` is slice and is like array (fixed size), immutable
- learned `String` is like `Vec`, backing store is `Vec`, mutable

## Day 5

- learned rust guarantees memory safety **without** a garbage collector
- learned about `let` and `const`
  - use `let` for immutable
  - use `let mut` for mutable
  - can assign variables to same name (multiple `let`):

```rust
fn main() {
    let myvar = 1;
    println!("{}", myvar);
    let myvar = "3";
    println!("{}", myvar);
}
```

- learned about ownership
  - passing value to method gives up ownership. cannot pass into a subsequent method
  - overcome by using `clone()`

```rust
use std::{collections::HashMap, fs::read_to_string};

fn main() {
    let source = read_to_string("./README.md").unwrap();
    let mut files = HashMap::new();
    files.insert("README", source.clone());
    files.insert("README2", source);
}
```

- learned about borrowing
  - use `&` to take a reference, prevents copying
  - can only borrow mutable one at a time. **ordering matters**

```rust
    let files_ref = &mut files;

    needs_mutable_ref(files_ref);

    let files_ref2 = &mut files;

    needs_mutable_ref(files_ref2);
```

- learned about `{:?}` as a debug formatter in strings

```rust
println!("{:?}", map)
```

- learned that in JS objects are passed to functions by reference, while others are passed by value
  - by reference means changes to it change the item globally
  - by value means changes to it do not change the item globally

```
JS Results:

String before function: Hello!
String in function: Hello! What a nice day.
String after function: Hello!

Number before function: 2000
Number in function: 2001
Number after function: 2000

Object before function: Jane Doe
Object in function: Samuel Clemens
Object after function: Samuel Clemens
```

## Day 6

- learned about `&str`
  - borrowed string slices
  - rust can optimize away duplicates
- learned about `String`
  - use `to_owned()` to convert `&str` to `String`
    - `to_owned` can be used for other types to go from reference to value version
  - using `to_string` works on all that implement `Display`
- learned about `to_owned` variants. prefer `to_owned()`
  - `String::from`
    - calls `to_owned`
  - `to_string()`
    - calls `String::from`
  - `.into()`
    - calls `String::from()`
  - `format!()`
    - calls `Display::fmt`

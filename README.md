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

```
"rust-analyzer.checkOnSave.command": "clippy"
```

- optional remove line hints with:

```
{
  "rust-analyzer.inlayHints.enable": false,
  "rust-analyzer.inlayHints.chainingHints": false,
  "rust-analyzer.inlayHints.parameterHints": false
}
```

- optional remove auto download

```
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

```
println!("{}", greet)
```
- learned `&str` is slice and is like array (fixed size), immutable
- learned `String` is like `Vec`, backing store is `Vec`, mutable
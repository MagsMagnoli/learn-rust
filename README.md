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

## Day 7

**Style**

- variables, functions, modules in snake case `time_in_millis`
- structs are pascal `CpuModel`
- globals upper snake: `GLOBAL_TIMEOUT`
- unabmbiguous parenthesis optional

```rust
if x > y { /* */ }

while x > y { /* */ }
```

**Expressions**

- almost everything an expression, has return value

```rust
fn main() {
    let apples = 6;
    let message = if apples > 10 {
        "Lots of apples"
    } else if apples > 4 {
        "A few apples"
    } else {
        "Not many apples at all"
    };

    println!("{}", message) // prints "A few apples"
}
```

**Unit type**

- no `null` or `undefined` types
- `()` represents no value, unit type

**Implicit returns**

- last line used as return value in function if no semi-colon
- `return` often absent

```rust
fn add_numbers(left: i64, right: i64) -> i64 {
    left + right  // ⬅ Notice no semi-colon
}
```

**Arrays**

- rust array `[]` are immutable and must have known size
- `Vec` is mutable array at end
- `VecDeque` is mutable array at both ends
  - 'vec-deck'
  - 'deque' = double-ended queue

```rust
let mut numbers = vec![1, 2, 3, 4, 5];  // ⬅ Notice the vec! macro
numbers.push(7);
println!("{:?}", numbers);
```

## Day 8

**HashMap**

- hashmap returns `Option` enum since no `null` / `undefined`
  - values are `Some` and `None`
  - can test with `.is_some()` and `.is_none()`
  - can unwrap unsafely with `.unwrap()`
  - can unwrap safely wtih `.unwrap_or_()`

```rust
use std::collections::HashMap;

fn main() {
  let mut map = HashMap::new();
  map.insert("key1", "value1");
  map.insert("key2", "value2");

  println!("{}", map.get("key1").unwrap_or(&""));
  println!("{}", map.get("key2").unwrap_or(&""));
}

```

- returns borrowed value
  - if already `&str` then returns `&&str`

**Structs**

- define objects as structs

```rust
struct TrafficLight {
  color: String,
}

let light = TrafficLight {
  color: "red".to_owned(), // Note we want an owned String
};
```

- use `impl` to add behavior

```rust
impl TrafficLight {
  pub fn new() -> Self {
    Self {
      color: "red".to_owned(),
    }
  }
}

fn main() {
  let light = TrafficLight::new();
}
```

- can add trait to support printing values

```rust
impl std::fmt::Display for TrafficLight {
  fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
    write!(f, "Traffic light is {}", self.color)
  }
}
```

- can derive implementation of trait if all impement specified trait

```rust
#[derive(Debug)]
struct TrafficLight {
  color: String,
}
```

## Day 9

- static function on struct

```rust
impl TrafficLight {
  pub fn new() -> Self {
    Self {
      color: "red".to_owned(),
    }
  }
}
// let light = TrafficLight::new()
```

- methods are private by default
  - need `self` or `&self` as first argument to be public
- using `self` gives up ownership of values after first call. useful for:
  - conversions
  - cleanup code
  - builder patterns

```rust
pub fn get_state(&self) -> &String {
  &self.color
}
```

- mutate state with `&mut self` param

```rust
pub fn turn_green(&mut self) {
  self.color = "green".to_owned()
}

let mut light = TrafficLight::new();
```

**Enums**

```rust
enum TrafficLightColor {
  Red,
  Yellow,
  Green,
}
```

- add `#[derive(Debug)]` to enable debug
- implement display using `match` expression

```rust
impl Display for TrafficLightColor {
  fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
    let color_string = match self {
      TrafficLightColor::Green => "green",
      TrafficLightColor::Red => "red",
      TrafficLightColor::Yellow => "yellow",
    };
    write!(f, "{}", color_string)
  }
}
```

- `write!` is macro used like `print!` for display

## Day 10

- traits define methods
  - can have body for default implementation
- implement `trait` via `impl [trait] for [struct]` format

```rust
trait Light {
  fn get_name(&self) -> &str;
}

impl Light for HouseLight {
  fn get_name(&self) -> &str {
    "House light"
  }
}
```

- use `dyn [trait]` when rust cant know value's type at compile time
  - loses type information

```rust
trait Light {
  fn get_name(&self) -> &str;
  fn get_state(&self) -> &dyn std::fmt::Debug;
}

impl Light for HouseLight {
  fn get_name(&self) -> &str {
    "House light"
  }

  fn get_state(&self) -> &dyn std::fmt::Debug {
    &self.on
  }
}

impl Light for TrafficLight {
  fn get_name(&self) -> &str {
    "Traffic light"
  }

  fn get_state(&self) -> &dyn std::fmt::Debug {
    &self.color
  }
}
```

- `dyn [trait]` are unsized. `&dyn [trait]` are sized

## Day 11

**Import files**

- rust imports via modules, not file names
- create modules with `mod my_module;` format
  - does not need to be in file named after it

**Import functions**

- two main styles, direct and via `use`
- use `{}` to import multiple
- use `super` to import from parent
- use `crate` to import starting from crate root

```rust
// import direct
some_module::some_function();

// import use
use some_module::some_function;
some_function()

// import multiple
use std::io::{Read, Write};

// super
fn work() {}

mod test {
	use super::work;
}

// crate
use crate::some_module::some_function
```

**Rust module system**

- rust project is crate
- crate root is entrypoint, usually `main.rs` or `lib.rs`
- rust file lookup order
  - if import is `one::two::three` looks for `./one/two/three.rs` or `./one/two/three/mod.rs`
    - `mod.rs` not recommended anymore

**Visibility**

- private by default except enums and traits
- visibility up and out
  - deeper definitions not seen unless changed
- use `pub` to make visible
- pub modifiers
  - `pub(crate)` for within crate visibility
  - `pub(super)` for parent module only

## Day 12

- generic function accept `toString`

```rust
fn needs_string<T: ToString>(almost_string: T) {
  let real_string = almost_string.to_string();
  println!("{}", real_string);
}
```

## Day 13

**Enums**

- option returns `Some` or `None`

```rust
pub enum Option<T> {
    /// No value
    None,
    /// Some value `T`
    Some(T),
}
```

- result returns `Ok` or `Err`

```rust
pub enum Result<T, E> {
  Ok(T),
  Err(E),
}
```

**Getting the value**

- `.unwrap()`
  - only use when sure of `Some` or `Ok`, otherwise code will panic / crash

```rust
let ip_address = std::net::Ipv4Addr::from_str("127.0.0.1").unwrap();
```

- `.unwrap_or()`
  - use to give default value in case of failure

```rust
let default_string = "Default value".to_owned();

let unwrap_or = returns_none().unwrap_or(default_string);

println!("returns_none().unwrap_or(...): {:?}", unwrap_or);
```

- `.unwrap_or_else()`
  - like `.unwrap_or()` but takes function
  - `|| ...` is rust closure syntax

```rust
let unwrap_or_else = returns_none()
  .unwrap_or_else(|| format!("Default value from a function at time {:?}", Instant::now()));

println!(
  "returns_none().unwrap_or_else(|| {{...}}): {:?}",
  unwrap_or_else
);
```

- `.unwrap_or_default()`
  - defer to types default

```rust
let my_string = maybe_none.unwrap_or_default(); // Assuming `T` is `String`.
```

- `match`
  - match enum variants and return value

```rust
let match_value = match returns_some() {
  Some(val) => val,
  None => "My default value".to_owned(),
};

println!("match {{...}}: {:?}", match_value);
```

- `if let`
  - enter a block conditionally

```rust
if let Some(val) = returns_some() {
  println!("if let : {:?}", val);
}
```

- `?` unwrapping
  - use `?` at end of statement to unwrap and exit early on error

```rust
use std::fs::read_to_string;

fn main() -> Result<(), std::io::Error> {
  let html = render_markdown("./README.md")?;
  println!("{}", html);
  Ok(())
}

fn render_markdown(file: &str) -> Result<String, std::io::Error> {
  let source = read_to_string(file)?;
  Ok(markdown::to_html(&source))
}
```

- if `read_to_string()` results in error
  - returns error immediately to `main`
  - `render_markdown()` returns error immediately, since top level causes crash

## Day 14

**Error handling with Box**

- `dyn [trait]` has no type, rust can't know size so errors
- `Box<dyn Error>` allows to place a value _somewhere_ and have a reference to it
- **Use sparingly**: Result doesn't constrain error's type and will have issues when error doesn't implement `Error`

```rust
use std::{error::Error, fs::read_to_string};

fn main() -> Result<(), Box<dyn Error>> {
  let html = render_markdown()?;
  println!("{}", html);
  Ok(())
}

fn render_markdown() -> Result<String, Box<dyn Error>> {
  let file = std::env::var("MARKDOWN")?;
  let source = read_to_string(file)?;
  Ok(markdown::to_html(&source))
}
```

**Error handling with custom error type**

- errors can be structs or enums
- need to implement `Debug` and `Display`
- may need to implement `From`, `Into`, `TryFrom`, `TryInto` for conversion

```rust
use std::fs::read_to_string;

fn main() -> Result<(), MyError> {
  let html = render_markdown()?;
  println!("{}", html);
  Ok(())
}

fn render_markdown() -> Result<String, MyError> {
  let file = std::env::var("MARKDOWN")?;
  let source = read_to_string(file)?;
  Ok(markdown::to_html(&source))
}

#[derive(Debug)]
enum MyError {
  EnvironmentVariableNotFound,
  IOError(std::io::Error),
}

impl From<std::env::VarError> for MyError {
  fn from(_: std::env::VarError) -> Self {
    Self::EnvironmentVariableNotFound
  }
}

impl From<std::io::Error> for MyError {
  fn from(value: std::io::Error) -> Self {
    Self::IOError(value)
  }
}

impl std::error::Error for MyError {}

impl std::fmt::Display for MyError {
  fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
    match self {
      MyError::EnvironmentVariableNotFound => write!(f, "Environment variable not found"),
      MyError::IOError(err) => write!(f, "IO Error: {}", err.to_string()),
    }
  }
}
```

**Crate for error handling**

- [thiserror](https://docs.rs/thiserror/latest/thiserror/) gives custom error solution with less boilerplate

```rust
use std::fs::read_to_string;

fn main() -> Result<(), MyError> {
  let html = render_markdown()?;
  println!("{}", html);
  Ok(())
}

fn render_markdown() -> Result<String, MyError> {
  let file = std::env::var("MARKDOWN")?;
  let source = read_to_string(file)?;
  Ok(markdown::to_html(&source))
}

#[derive(thiserror::Error, Debug)]
enum MyError {
  #[error("Environment variable not found")]
  EnvironmentVariableNotFound(#[from] std::env::VarError),
  #[error(transparent)]
  IOError(#[from] std::io::Error),
}
```

- [errorchain](https://docs.rs/error-chain/0.12.4/error_chain/index.html) gives `Error` struct, `ErrorKind` enum, and custom `Result` type aliased

```rust
use std::fs::read_to_string;

error_chain::error_chain! {
  foreign_links {
    EnvironmentVariableNotFound(::std::env::VarError);
    IOError(::std::io::Error);
  }
}

fn main() -> Result<()> {
  let html = render_markdown()?;
  println!("{}", html);
  Ok(())
}

fn render_markdown() -> Result<String> {
  let file = std::env::var("MARKDOWN")?;
  let source = read_to_string(file)?;
  Ok(markdown::to_html(&source))
}
```

- [anyhow](https://github.com/dtolnay/anyhow) for simple error handling for basic / personal applications when you don't care to give caller all the info they need on the error

**Other crates**

- [snafu](https://docs.rs/snafu/0.6.10/snafu/index.html)
- [quick-error](https://docs.rs/quick-error/latest/quick_error/)
- [failure](https://docs.rs/failure/latest/failure/)
- [err-derive](https://docs.rs/err-derive/0.3.0/err_derive/)

## Day 15

- rust closures use pipes for arguments with no separator between arguments and body

```rust
let closure = || {
  println!("Hi! I'm in a closure");
};
closure();
```

- shorthand closure omits braces

```rust
let double = |num: i64| num + num;
let num = 4;
println!("{} + {} = {}", num, num, double(num));
```

- closures must be marked mutable to update state

```rust
let mut counter = 0;

let mut closure = || {
  counter += 1;
  println!(
    "This closure has a counter. I've been run {} times.",
    counter
  );
};
closure();
closure();
closure();
println!("The closure was called a total of {} times", counter);
```

- returning closures
  -function flavors
  - `Fn` - immutably borrows any variables
  - `FnMut` - mutably borrows any variables
  - `FnOnce` - consumes its values, can only be run once
- `move` keyword indicates ownership of any variables referenced

```rust
fn make_adder(left: i32) -> impl Fn(i32) -> i32 {
  move |right: i32| {
    println!("{} + {} is {}", left, right, left + right);
    left + right
  }
}

let plus_two = make_adder(2);
plus_two(23);
```

- function composition

```rust
fn compose<T>(f: impl Fn(T) -> T, g: impl Fn(T) -> T) -> impl Fn(T) -> T {
  move |i: T| f(g(i))
}

let plus_two = make_adder(2);  // ← make_adder from above
let times_two = |i: i32| i * 2;
let double_plus_two = compose(plus_two, times_two);
println!("{} * 2 + 2 = {}", 10, double_plus_two(10));
```

- regular function references

```rust
fn regular_function() {
  println!("I'm a regular function");
}

let fn_ref = regular_function;
fn_ref();
```

- closures in structs

```rust
struct DynamicBehavior<T> {
  closure: Box<dyn Fn(T) -> T>,
}

impl<T> DynamicBehavior<T> {
  fn new(closure: Box<dyn Fn(T) -> T>) -> Self {
    Self { closure }
  }
  fn run(&self, arg: T) -> T {
    (self.closure)(arg)
  }
}

let square = DynamicBehavior::new(Box::new(|num: i64| num * num));
println!("{} squared is {}", 5, square.run(5))
```

## Day 16: Lifetimes, references, and 'static'

- lifetime: every value has point of created and point of dropped
- lifetime annotation: syntax to add to give lifetime a named tag. needed to disambiguate
- all references have lifetimes and implicit annotations even if not written explicitly

```rust
fn omits_annotations(list: &[String]) -> Option<&String> {
  list.get(0)
}

fn has_annotations<'a>(list: &'a [String]) -> Option<&'a String> {
  list.get(1)
}
```

- static lifetime usually used
  - as explicit lifetime annotation
  - as lifetime bounds on generic type

```rust
fn main() {
  let mark_twain = "Samuel Clemens";
  print_author(mark_twain);
}
fn print_author(author: &'static str) {
  println!("{}", author);
}

fn print<T: Display + 'static>(message: &T) {
  println!("{}", message);
}
```

- `&'static` means reference valid for rest of program. wont move or change
  - string literals are `&'static`
  - says nothing about lifetime
  - if you need to add `&'static` to make things work, rethink
  - if you need to add `'static` bound it's likely ok

## Day 17: Arrays, Loops, and Iterators

- arrays via `[]` cannot change size
- use `vec![]` for array that can grow at end

**Loops**

- no standard `for (...; ...; ...;)` syntax
- create loops via `for i in (range)` where `i` is nameable var and `(range)` is a range like `0..10`
- cannot iterate object keys automatically. can use `.keys()` on HashMap to get an iterator
- can use `for...in` on arrays

**while (!done)**

- can use `while let` syntax to loop until some condition is false

```rust
struct Worker {
  data: Vec<&'static str>,
}
impl Worker {
  fn doWork(&mut self) -> Option<&'static str> {
    self.data.pop()
  }
}
let mut obj = Worker {
  data: vec!["a", "b", "c"],
};

while let Some(data) = obj.doWork() {
  println!("{}", data);
}
```

- no `do...while`
- `loop` expression to loop forever like `while (true)`. exit with `break`

```rust
let mut n = 0;
loop {
  n += 1;
  if n > 3 {
    break;
  }
}
println!("Finished. n={}", n);
```

**Labels**

- labels must begin with `'`

```rust
'outer: loop {
  loop {
    break 'outer;
  }
}
```

**Break and Loop expressions**

- can set value with break in a loop

```rust
let value = loop {
  if true {
    break "A";
  } else {
    break "B";
  }
};
println!("Loop value is: {}", value);
```

**Iterators**

- rust iterators are lazy
- has `Item` type as placeholder for items in interation
- call `.iter()` to get and use iterator on `Vec`
- call `.collect()` to consume iterator

```rust
let list = vec![1, 2, 3];
let doubled: Vec<_> = list
  .iter()
  .map(|num| num * 2)
  .collect();
println!("{:?}", doubled);
```

- gotcha: `type annotations needed`

  - rust knows types in iterator but needs to know output type
  - `Vec<_>` tells rust to output a `Vec` with known types in iterator

- use `iter_mut` to mutably borrow elements

`.filter()`

- use `*` to dereference double reference created by `.iter()` and `.filter()`

```rust
let numbers = [1, 2, 3, 4, 5];
let even: Vec<_> = numbers.iter().filter(|x| *x % 2 == 0).collect();
println!("{:?}", even);
```

`.find()`

- can store iterator and call `.find()` multiple times

```rust
let numbers = [1, 2, 3, 4, 5];
let first_even = numbers.iter().find(|x| *x % 2 == 0);
println!("{:?}", first_even.unwrap());
```

`.forEach()`

- consumes iterator immediately

```rust
let numbers = [1, 2, 3];
numbers.iter().for_each(|x| println!("{}", x));
```

`.join()`

```rust
let names = ["Sam", "Janet", "Hunter"];
let csv = names.join(", ");
println!("{}", csv);
```

`.map()`

```rust
let list = vec![1, 2, 3];
let doubled: Vec<_> = list.iter().map(|num| num * 2).collect();
println!("{:?}", doubled)
```

`.push()` and `.pop()`

- only available on `Vec` types, not regular arrays
- become `.push_back()` and `.pop_back()` on `VecDeque`

```rust
let mut list = vec![1, 2];
list.push(3);
println!("{:?}", list.pop());
```

`.shift()` and `.unshift()`

- need `VecDeque` to push/pop from front

```rust
let mut list = VecDeque::from([1, 2]);
list.push_front(0);
println!("{:?}", list.pop_front());
```

**Returning iterators**

- best practice to return iterator rather than final type
  - maintains lazy nature
  - allows for easy chaining

```rust
struct Names {
  names: Vec<String>,
}

impl Names {
  fn search<T: AsRef<str>>(&self, re: T) -> impl Iterator<Item = &String> {
    let regex = regex::Regex::new(re.as_ref()).unwrap();
    self.names.iter().filter(move |name| regex.is_match(name))
  }
}
```

## Day 18: Async

- rust gives async tasks via `Future` and `await` but no executor and reactor to notify when futures are done

**Rust Async Libraries**

- [Tokio](https://crates.io/crates/tokio)
- [Async-std](https://crates.io/crates/async-std)
- [Smol](https://crates.io/crates/smol)

**Using Tokio**

- add dependency with `full` feature flag

```rust
[dependencies]
tokio = { version = "1", features = ["full"] }
```

- must start executor before futures

```rust
#[tokio::main]
async fn main()  { // Notice we write async main() now
}
```

- `async` turns function return value from `T` to `impl Future<Output = T>`
- `await` must be appended to future
- **futures don't run until awaited**

```rust
#[tokio::main]
async fn main() {
    let msg = async_fn().await;
}
```

- can create async closures by returning async blocks

```rust
#[tokio::main]
async fn main() {
    let msg = "Hello world".to_owned();

    let closure = || async {
        println!("{}", msg);
    };
    closure().await;
}
```

**Send and Sync**

- `Send` and `Sync` determine if can be sent or accessed across threads safely
- can add via `+ Send` and `+ Sync`
- can add with `'static` to let rust know parameter can last forever

```rust
fn async_print<T: Display + Send + 'static>(msg: T) -> JoinHandle<()> {
    tokio::task::spawn(async move {
        println!("{}", msg);
    })
}

```

## More Learning

- [Rust Book](https://doc.rust-lang.org/stable/book/)

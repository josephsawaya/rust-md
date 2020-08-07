- [Chapter 1](#chapter-1)
- [Chapter 2 - Programming a guessing game](#chapter-2---programming-a-guessing-game)
- [Chapter 3 - Common Programming Concepts](#chapter-3---common-programming-concepts)
  - [3.1 Variables and Mutability](#31-variables-and-mutability)
  - [3.2 Data Types](#32-data-types)
    - [Integers](#integers)
    - [Floating-point types](#floating-point-types)
    - [Numeric Operations](#numeric-operations)
    - [Booleans](#booleans)
    - [Characters](#characters)
    - [Tuple](#tuple)
    - [Array](#array)
  - [3.3 Functions](#33-functions)
  - [3.4 Comments](#34-comments)
  - [3.5 Control Flow](#35-control-flow)
    - [If statements](#if-statements)
    - [Loops](#loops)
- [Chapter 4 - Ownership](#chapter-4---ownership)
  - [4.1 What is ownership?](#41-what-is-ownership)
    - [The Stack and the Heap](#the-stack-and-the-heap)
    - [Back to ownership](#back-to-ownership)
    - [Variable Scope](#variable-scope)
    - [The String type](#the-string-type)
    - [Memory and Allocation](#memory-and-allocation)
    - [Ways Variables and Data Interact: Move](#ways-variables-and-data-interact-move)
    - [Stack-only data: Copy](#stack-only-data-copy)
    - [Ownership and Functions](#ownership-and-functions)
    - [Return Values and Scope](#return-values-and-scope)
  - [4.2 References and Borrowing](#42-references-and-borrowing)
    - [Dangling references](#dangling-references)
  - [4.3 The Slice Type](#43-the-slice-type)
    - [String literals are slices](#string-literals-are-slices)
    - [String slices as parameters](#string-slices-as-parameters)
    - [Other slices](#other-slices)
- [Chapter 5 - Structs](#chapter-5---structs)
  - [5.1 Defining and instantiating structs](#51-defining-and-instantiating-structs)
    - [Struct update syntax](#struct-update-syntax)
    - [Tuple Structs without named fields](#tuple-structs-without-named-fields)
    - [Unit-like structs without any fields](#unit-like-structs-without-any-fields)
    - [Quick note](#quick-note)
  - [5.2 An example program using structs](#52-an-example-program-using-structs)
    - [Adding functionality using derived traits](#adding-functionality-using-derived-traits)
  - [5.3 The method Syntax](#53-the-method-syntax)
    - [Methods with More Parameters](#methods-with-more-parameters)
    - [Associated functions](#associated-functions)

# Chapter 1

To install rust on linux/mac type this:

`$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`

Updating rust is done like so:
`$ rustup update`

To uninstall rust run this:
`$ rustup self uninstall`

Installation of rust also contains docs that you can examine by running:
`$ rustup doc`

Create a new project using:
`$ cargo new <project-name>`

this is what file structure looks like:

```
├── Cargo.toml
└── src
  └── main.rs
```

You write code in main.rs

To compile that code run:

`$ rustc main.rs`

This will generate an executable titled _main.exe_

This is a simple hello world program:

```
fn main() {
    println!("Hello, world!");
}
```

A few notes:

- we are declaring a main function that will be the first thing that runs in our program
- println! is a rust macro as denoted by the exclamation mark
- also notice the semicolon

The cargo.toml should look like this:

```
[package]
name = "hello_cargo"
version = "0.1.0"
authors = ["Your Name <you@example.com>"]
edition = "2018"

[dependencies]
```

This is written in TOML, the package section denotes info needed for rust to compile your program and we will be using dependencies also
known as crates later

We can build this program using: `$ cargo build`

The difference between this and the `rustc` command is that `cargo build` will generate a _cargo.lock_ to keep track of our dependencies

We can then run our program by using: `$ cargo run`

`cargo run` will automatically build files that have changed

You can use `$ cargo check` to check if your code compiles without actually generating an executable

To build for release add `--release` to your `cargo build` command

In the next section we will program a guessing game:

# Chapter 2 - Programming a guessing game

Here is the code we will initially paste into _src/main.rs_:

```
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

The first line: `use std::io` is how we import the standard io library for rust

We can also see our main function and a couple println! macros

After this we use a `let` statement to create a variable to store information

By default variables in rust are immutable so that's why we use the `mut` keyword

On the other side of the `=` we see an instance of a String type being initialized, String is a type and the `::` indicates new is an
_associated function_ of the String type, an associated function is a function implemented ona type rather than an instance of that type

After creating the string we use the io from the standard library by doing `io::stdin()` which returns an instance of io::Stdin which represents
the standard input of our terminal, we then call the read_line method on this instance that will read one line of our standard input, this
method takes one argument which is a variable in which the input will be stored

The `&mut` is using a reference allowing the method to access the variable directly rather than making a copy, the mut is necessary here because
references are also immutable by default

The next part of the code is the `.expect("Failed to read line")` method

the _read_line_ call returns io::Result which is an enum that has two variants: _Ok_ and _Err_

_expect_ is a method on the io::Result instance that checks if the instance is _Ok_ or _Err_ and will return the return value of the orginal method or
display an error that we pass into expect respectively

In the next and final line we make use of println! placeholders the set of `{}` and list the variables in order of replacement as other arguments to
the method

For the second part of this program we will generate a random number for this we will make use of a crate as a dependency

In our _cargo.toml_ under the dependencies section we will add a new dependency:

```
[dependencies]
rand="0.5.5"
```

We specify the name and version of the dependency we want to use

When we cargo build now it will download and compile our dependency before compiling the project now containing these dependencies

This will also generate a _cargo.lock_ file that will lock dependencies to a certain version to use specific dependency versions

To update a dependency run `$ cargo update`, this will only update it in a limited way it will look for versions more than 0.5.5 but less than
0.6.0s

Now we must generate a random number, the code for doing this is as follows:

```
let secret_number = rand::thread_rng().gen_range(1, 101);
```

We also have to add `use rand::Rng` at the top of our file, this is a trait that defines methods that we will use to generate random numbers

We then add a line calling a method: `rand::thread_rng().gen_range(1,101);`

The first part `rand::thread_rng()` specifies we want to use a random number generator running on the local thread seeded by the operating system

The `gen_range(1,101)` specifies the bounds of the random number we want to generate, the lower bound is inclusive but the upper bound is exclusive

Now we want to compare the number the user inputs and the random number that we generated, for this we make use of this code:

```
//at the top of the file
use std::cmp::Ordering;

//at the end of our main
match guess.cmp(&secret_number) {
  Ordering::Less => println!("Too small!"),
  Ordering::Greater => println!("Too big!"),
  Ordering::Equal => println!("You win!"),
}
```

In the first part of this code we are bringing another type into the scope of our code: _Ordering_ it is an enum just like _Result_ and it
has 3 variants: _Lesser_, _Equal_ and _Greater_

We then use a match statement on an expression, the expression is quite simple we are calling the _cmp_ method on _guess_ which is of type number
and we are comparing _guess_ to our _secret_number_, this method returns an _Ordering_ enum

The match statement is somewhat like a switch statement in other languages, the way it works is that it will evaluate the expression next to it
then it will go through the _arms_ in order and if the evaluated expression matches the _pattern_ of that _arm_ it will run the code contained in
that _arm_

If we try to run this code it will not compile, the reason for this is that we are trying to compare a string to a number and rust has a strong
typing system

To fix this issue we must add this code to our program:

```
let guess: u32 = guess.trim().parse().expect("Please type a number!");
```

Rust allows us to shadow variables so we use this normally when we want to change the type of a variable, this is what we are doing here

We are calling the _trim_ method on the guess string and what this will do is get rid of all whitespace at the beginning and end of the string as well
as newlines

the _parse_ method allows us to turn the string into a number, we specify the type of the number on the left side of the expression by using
`: u32` we specify that _guess_ is supposed to be a 32-bit unsigned integer, this is called a type annotation,
also the compiler will now assume that _secret_number_ is going to be of type u32, _parse_ also returns a _Result_ enum so we will
need to call _expect_ afterwards to handle errors

We will now allow for the user to make multiple guesses by using a loop

The loop keyword creates an infinite loop:

```
loop {
  println!("Please input your guess.");

  let mut guess = String::new();

  io::stdin()
    .read_line(&mut guess)
    .expect("Failed to read line");

  let guess: u32 = guess.trim().parse().expect("Please input a number");

  println!("You guessed: {}", guess);

  match guess.cmp(&secret_number) {
    Ordering::Less => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal => println!("You win!"),
  }
}
```

this loop will go on until the user inputs an invalid input

However we want the program to end when the user wins so we can add one more line to make this happen:

```
  match guess.cmp(&secret_number) {
    Ordering::Less => println!("Too small!"),
    Ordering::Greater => println!("Too big!"),
    Ordering::Equal => {
      println!("You win!")
      break;
    }
  }
}
```

This will break out of the loop on win

Finally we want to handle invalid input in a better way, for this we will replace the expect when we're shadowing with a match statement:

```
let guess: u32 = match guess.trim().parse() {
  Ok(num) => num;
  Err(_) => continue;
}
```

We know that our _parse_ method returns a _Result_ so we are matching that _Result_ to see whether it returns _Ok_ or _Err_, if it's ok that _Ok_ enum will
contain data corresponding to the result of the method so we are going to return that data, the _Err_ will contian information about the error and we
are using a catch-all to say that no matter what data is contained in that enum if it is an error perform a _continue_ and start the loop over

The last step is to clean up and stop printing the secret number and the program is complete

# Chapter 3 - Common Programming Concepts

## 3.1 Variables and Mutability

You can declare a variable like this: `let x = 5;`, they are immutable by default

To make a variable mutable we must add `mut` to its declaration

We can declare constants using `const` instead of `let`

We can also use variable shadowing by repeating the let keyword, shadowing is useful if we want to use the same variable name for a variable of a different
type

We cannot use variable shadowing if a variable is declared as mutable

## 3.2 Data Types

There are two data type subsets: scalar and compound

Scalar type represents a single value, rust has 4 scalar types: integers, floating-point numbers, booleans, and characters

### Integers

Integers type variants are based on size of the integer, and whether the integer is signed or unsigned, ex: i32(signed 32-bit integer), u64(unsigned 64-bit integer)

possible sizes include: 8, 16, 32, 64, 128

Signed variant can store numbers from -2^(n-1) to 2^(n-1) - 1

Unsigned variant can store numbers from 0 to 2^n - 1

There are also isize and usize variants that depend on the architecture you are using

You can write integer literals in decimal, hex, octal, binary, and byte for u8

### Floating-point types

Rust has two primitives for floating-point numbers: f32 and f64, the default type is f64

example:

```
fn main(){
	let x = 2.0; //f64

	let y: f32 = 3.0; //f32
}
```

f32 is a single precision float and f64 has double precision

### Numeric Operations

Rust supports the basic operations for number types: addition, subtraction, multiplication, division, and remainder

### Booleans

Booleans are a byte in size, booleans are specified using `bool`

### Characters

Rust's char type is four bytes in size and represents a Unicode Scalar Value, however characters don't really exist in unicode so they just talk more about this later

### Tuple

Tuples are a way of grouping together a number of values with a variety of types into one compound type, once declared they cannot grow or shrink in size

ex:

```
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

The variable tup binds to the entire tuple so to extract values we can do this

```
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {}", y);
}
```

We can also address the element directly by using a period and the index of the value we want to access

index starts with zero

### Array

In rust values in an array are written as comma seperated inside square brackets:

```
fn main() {
    let a = [1, 2, 3, 4, 5];
}
```

Arrays are stored on the stack(static memory) and can't change in size

The way to write an array type is by using square brackets and inside write the type of each element a semicolon then the number of elements in the array

To access the elements in an array using indexing:

```
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```

Out of index array errors will produce a runtime error, you will never be able to access an array out index range rust will always throw an error

## 3.3 Functions

Important to note that rust uses snake case as the conventional style for functions and variable names

Function definitions in Rust start with `fn` and have a set of parentheses after the function name. The curly brackets tell the compiler where the function body begins and ends.

We can include function parameters like this:

```
fn main() {
    another_function(5);
}

fn another_function(x: i32) {
    println!("The value of x is: {}", x);
}
```

We must declare the type of the parameter, seperate multiple parameters with commas
Functions contain statements and expressions, statements do not have return values whereas expressions do have return values

We can use expressions in this way:

```
fn main() {
    let x = 5;

    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {}", y);
}
```

We can see that we create a new scope and in that scope we declare a new variable and at the of the scope there is no semicolon so what is returned is x+1

Functions with return values, functions can return a value and this is how we declare a function that will return a value:

```
fn five() -> i32 {
    5
}

fn main() {
    let x = five();

    println!("The value of x is: {}", x);
}
```

We use the `->` between the parentheses and the squiqly brackets, also notice no `return` keyword, instead there is just no semicolon at the end of the function

This is because with no semicolon it is an expression and not a statement

## 3.4 Comments

Rust comments are made using the `//` and everything written from the `//` until the end of the line will be commented

## 3.5 Control Flow

### If statements

This is how you write an if statement:

```
fn main() {
    let number = 3;

    if number < 5 {
        println!("condition was true");
    } else {
        println!("condition was false");
    }
}
```

Rust does not automatically convert non-Boolean to Boolean types

Using `else if`

```
fn main() {
    let number = 6;

    if number % 4 == 0 {
        println!("number is divisible by 4");
    } else if number % 3 == 0 {
        println!("number is divisible by 3");
    } else if number % 2 == 0 {
        println!("number is divisible by 2");
    } else {
        println!("number is not divisible by 4, 3, or 2");
    }
}
```

Using too many `else if` can clutter so in some cases it can be better to use a match statement

Using conditional expressions in `let`:

```
fn main() {
    let condition = true;
    let number = if condition { 5 } else { 6 };

    println!("The value of number is: {}", number);
}
```

The types must be the same or else rust throws an error ex:

`let number = if condition { 5 } else { "six" };`

### Loops

Rust has 3 types of loops: `loop`, `while` and `for`

`loop` is an infinite loop that runs the same code until you tell it to stop:

```
fn main() {
    loop {
        println!("again!");
    }
}
```

Returning values from a `loop`, put the expression for the value you want to return after the `break` keyword:

```
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {}", result);
}
```

The `while` is a conditional loop that is used like this:

```
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}!", number);

        number -= 1;
    }

    println!("LIFTOFF!!!");
}
```

The `for` loop is used like this:

```
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a.iter() {
        println!("the value is: {}", element);
    }
}
```

We can also use Ranges for `for` loops like this:

```
fn main() {
    for number in (1..4).rev() {
        println!("{}!", number);
    }
    println!("LIFTOFF!!!");
}
```

# Chapter 4 - Ownership

## 4.1 What is ownership?

Ownership enables Rust to make memory safety guarantees without needing a garbage collector

We are going to talk about ownership and other related features: borrowing, slices, and how Rust organizes data in memory

Rust manages memory through a system of ownership with a set of rules the compiler checks at runtime, none of the ownership features slow down the program while it's running

### The Stack and the Heap

Parts of ownership can be described in relation to the stack and the heap

The stack and the heap are parts of memory that are available to the code at runtime but they are structure in different ways

The stack stores values in the order it gets them and removes the values in the opposite order this is reffered to as last in, first out, Adding data is called pushing onto the stack and removing data is called popping off the stack

All data stored on the stack must have a known fixed size, data with an unknown size or a sizer that might change must be stored on the heap instead

The heap is less organized when you put data on the heap you request a certain amount of space

The memory allocator finds a spot to put that data on the heap and returns a pointer to that data

That pointer is stored on the stack because it's size is known

Pushing to the stack is faster than allocating on the heap because the allocator never has to find a spot to store that data

Accessing data in the heap is slower than accessing data on the stack as well because you have to follow a pointer to get there

### Back to ownership

Ownership rules:

- Each value in rust has a variable that's called its owner
- There can only be one owner at a time
- When the owner goes out of scope, the value will be dropped

### Variable Scope

This is how scope works:

```
fn main() {
    {                      // s is not valid here, it’s not yet declared
        let s = "hello";   // s is valid from this point forward

        // do stuff with s
    }                      // this scope is now over, and s is no longer valid
}
```

When _s_ comes into scope it is valid and it's valid until it goes out of scope

### The String type

We can use string literals to declare a string but that string will be immutable

Rust has a second string type: `String` This type is allocated on the heap and is able to store an amount of text that is unknown to us at compile time:`let s = String::from("hello");`

This type of string can be mutated unlike string literals

### Memory and Allocation

In the case of s tring literal we know the contents at compile time so the text is harcoded directly into the final executable, this makes it immutable

With the `String` type in order to support a mutable growable piece of text: - The memory must be requested from the memory allocator at runtime - We need a way of returning this memory to the allocator when we are done with the `String`
The first part is done by us when we call `String::from`

The second part is different however in languages with a garbage collector the GC cleans memory that isn't being used anymore and we don't have to think about it, without a GC it becomes our responsibility

Rust takes a different path: memory is automatically returned once the variable that owns it is out of scope:

```
fn main() {
    {
        let s = String::from("hello"); // s is valid from this point forward

        // do stuff with s
    }                                  // this scope is now over, and s is no
                                       // longer valid
}
```

When a variable goes out of scope rust automatically calls a special function for us called `drop`

### Ways Variables and Data Interact: Move

Let's say we have this code:

```
fn main() {
    let x = 5;
    let y = x;
}
```

Rust will push two values equal to 5 onto the stack

Let's do the same with `String`

```
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;
}
```

In this case when we create `s1` we are allocating memory in the heap and pushing a `String` onto the stack the `String` on the stack consists of a pointer to where the string in the heap is, the length in bytes that the string in the heap is using and the capacity which we will talk about more later

When we assign `s2 = s1`, we are not allocating another string in the heap we are in fact just pushing another `String` to the stack containing the same information as the `String` we pushed for `s1`

Another thing is that when we assign `s2 = s1` we invalidate `s1`, we call this moving `s1` into `s2` this problem solves the double free error, it will only `drop` when `s2` goes out of scope

If we want to do a _deep copy_ we can use `clone` like so:

```
let s1 = String::from("hello");
let s2 = s1.clone();
```

### Stack-only data: Copy

So why is it that when we were doing the integer assignment we didn't have to use `clone`?
The reason is that it doesn't actually take that much time to deep copy an integer as well as other types

Rust has a special annotation called the `Copy` trait that we can place on types like integers that are stored on the stack

If a type has the `Copy` trait an older variable is still usable after assignment, if the type has the `Drop` trait it cannot have the `Copy` trait.

Types with `Copy` trait:

- All the integer types, such as u32.
- The Boolean type, bool, with values true and false.
- All the floating point types, such as f64.
- The character type, char.
- Tuples, if they only contain types that are also Copy. For example, (i32, i32) is Copy, but (i32, String) is not.

### Ownership and Functions

Passing a value to a function is similar to assigning a value to a variable, it will either move or copy.

Example:

```
fn main(){
    let s = String::from("hello");

    takes_ownership(s);
}

fn takes_ownership(some_string: String) {
    println!("{}", some_string);
}

```

In this example when we pass `s` to `takes_ownership` it is a move operation therefore `s` becomes invalid in the main scope. After going out of scope in `takes_ownership` drop will be called on `some_string` and the memory is freed.

### Return Values and Scope

Returning values can also transfer ownership

```
fn main() {
    let s1 = gives_ownership();
}

fn gives_ownership() -> String {
    let some_string = String::from("hello");

    some_string
}
```

In this example `gives_ownership` will initialize `some_string`, when returned it will move our to the calling function in this case it is moved to `s1`

Taking ownership and returning ownership with every function is tedious, what if we want to pass in a value but not let the function take ownership, for this we will use

## 4.2 References and Borrowing

Here's a snippet of code that uses reference instead of ownership when passing in a value:

```
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {
    s.len()
}
```

Notice that we are passing in `&s1` into the `calculate_length` call in main and expecting a `&String`

These ampersands are references this way `s` ends up pointing at `s1` in memory

The opposite of referencing is derefencing and this is teh dereference operator: `*`

The `&s1` syntax creates a reference that refers to the value of s1 but does not own it since it does not own it the value it points to will no tbe dropped when the reference goes out of scope

When functions have references as parameters instead of actual values, we won't need to return the values in order to give back ownership

This is called borrowing

We cannot modify a borrowed value however unless we make is a mutable reference:

```
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

First `s` in main must be mutable, then we had to pass in a mutable reference `&mut s` and make the function `change` accept a mutable reference to a `String`: `&mut String`

Mutable references have one big restriction you can have only one mutable reference to a particaular piece of data in a particular scope, ex:

```
    let mut s = String::from("hello");

    let r1 = &mut s;
    let r2 = &mut s;

    println!("{}, {}", r1, r2);
```

This piece of code above causes a compile-time error

This code below also causes an error as we cannot combine mutable and immutable references:

```
    let mut s = String::from("hello");

    let r1 = &s; // no problem
    let r2 = &s; // no problem
    let r3 = &mut s; // BIG PROBLEM

    println!("{}, {}, and {}", r1, r2, r3);
```

However a reference's scope starts from where it is introduced and continues through the last time it is used, this compiles:

```
    let mut s = String::from("hello");

    let r1 = &s; // no problem
    let r2 = &s; // no problem
    println!("{} and {}", r1, r2);
    // r1 and r2 are no longer used after this point

    let r3 = &mut s; // no problem
    println!("{}", r3);
```

### Dangling references

Rust guarantees that references will never be dangling references, the compiler will ensure that the data will not go out of scope before the reference to the data does

Here's an example of code that would normally create a dangling reference:

```
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s
}
```

Because `s` is declared inside `dangle` when the call to `dangle` is finished the contents of `s` will be dropped, this means that we would be returning a reference to an invalid `String`, Rust won't let us do this, instead we shouldn't return a reference to this `String` but rather the `String` itsself

**Recap**

- **At any given time, you can have either one mutable reference or any number of immutable references**
- **References must always be valid**

## 4.3 The Slice Type

Another data type that does not have ownership is the `slice`, Slices let you reference a contiguous sequence of elements in a collection rather than the whole collection

Here's a programming problem: write a function that takes a string and returns the first word it finds in that string, if the function doesn't find a space in the string, the whole string must be one word, so the entire string should be returned

For this we can use `String slices`, it is a reference to a part of a `String`:

```
   let s = String::from("hello world");

    let hello = &s[0..5];
    let world = &s[6..11];
```

Bracket syntax: `[starting_index..ending_inded]` `starting_index` being the first position in the slice and `ending_index` being one more than the last position in the slice

With Rust's `..` range syntax if you wan to start at the first index you can drop the value before the 2 periods, if you want to include the last byte of the `String` you can dropthe trailing number

You can also drop both values to take a slice of the entire `String`

Important to note that the string slice type is `&str`

Back to our programming problem we can use string clices to solve this:

```
fn first_word(s: &String) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

Now if we try to clear the `String` we pass into this function afterwards it will return a compile-time error:

```
fn main() {
    let mut s = String::from("hello world");

    let word = first_word(&s);

    s.clear(); // error!

    println!("the first word is: {}", word);
}
```

`s.clear()` requires a mutable borrow but we already made an immutable borrow that we use later in the program therefore this doesn't compile

### String literals are slices

when we declare `let s = "Hello, world!";` the type of `s` is `&str`, it's a slice pointing to that specificm point of the binary, and `&str` is an immutable reference

### String slices as parameters

when declaring the function `first_word` we should expect `s` passed in to be of type `&str` instead of being of type `&String` this is because if we have a string slice we can pass that directly, if we have a `String` we can pass a slice of our entire `String`

by doing this we don't lose any functionality and we make our function more general

### Other slices

String slices are only specific to strings, let's say we want to refer to a part of an array we would do it like so:

```
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];
```

This slice has a type of &[i32]

# Chapter 5 - Structs

## 5.1 Defining and instantiating structs

Structs are similar to tuples which were discussed in Chapter 3, however in a struct you name each piece of data so it's more clear what the values mean, because of this structs are more flexible than tuples, you don't have to rely on the order of the data to specify or access the values of an instance

This is how we define a struct:

```
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}
```

To use a struct we must create an instace of the struct like so:

```
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
```

The struct definition is a general template for the type and instances fill that template with particular data to create values of the type

To get a specific value from a struct we use dot notation:

```
let mut user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
user1.email = String::from("anotheremail@example.com");
```

Note that the instance of the user is mutable in this case

When using functions to create and return instances of structs we can use the field init shorthand:

```
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

### Struct update syntax

```
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    ..user1
};
```

The syntax `..` specifies that the remaining fields not explicitly set should have the same value as the fields in the given instance

### Tuple Structs without named fields

Here is the syntax to define a tuple struct

```
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);
```

Notice that `black` and `origin` are different types

### Unit-like structs without any fields

You can also define structs without any fields that are called unit-like structs
This is useful for implementing traits ona type but don't have any data that you want to store in the type itself

### Quick note

Using references inside structs requires the use of lifetimes

## 5.2 An example program using structs

let's write a program that calculates the area of a rectangle

```
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        area(&rect1)
    );
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```

### Adding functionality using derived traits

We want to be able to pring an instance of a rectangle and see it's values for debugging purposes like so:

```
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {}", rect1);
}
```

This won't work however and throws an rerror telling us that we haven't implemented `std::fmt::Display`, structs don't come witht this implemented, the error tells us that we can use `{:?}` instead of `{}` in our string however

The `:?` is a specifier telling `println!` we want to use an output format called `Debug`

This still doesn't work because we haven't implemented the `std::fmt::Debug` trait either

As a shortcut we can add `#[derive(Debug)]` just before our struct definition to opt in to Rust's built in functionality to print out debugging information

```
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}
```

To make this output look prettier we can use the `:#?` specifier instead of the `:?` specifier

## 5.3 The method Syntax

methods are different from functions in that they’re defined within the context of a struct (or an enum or a trait object, which we cover in Chapters 6 and 17, respectively), and their first parameter is always self, which represents the instance of the struct the method is being called on

Here we implement the `area` function on the struct instead of outside the struct:

```
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
}
```

We start an `impl` block and move the area function definition inside that block passing a parameter of `&self` intothat function

Methods can take ownership of `self`, borrow `self` immutably or borrow `self` mutably

### Methods with More Parameters

example:

```
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}
```

### Associated functions

Associated functions are often used for constructors that will return a new instace of the struct, example:

```
impl Rectangle {
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }
}
```

To call this function we do: `let sq = Rectangle::square(3);`

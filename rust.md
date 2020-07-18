# Chapter 1
To  install rust on linux/mac type this:

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

This will generate an executable titled *main.exe*

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

The difference between this and the `rustc` command is that `cargo build` will generate a *cargo.lock* to keep track of our dependencies

We can then run our program by using: `$ cargo run`

`cargo run` will automatically build files that have changed

You can use `$ cargo check` to check if your code compiles without actually generating an executable

To build for release add `--release` to your `cargo build` command

In the next section we will program a guessing game:

# Chapter 2
Here is the code we will initially paste into *src/main.rs*:
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
*associated function* of the String type, an associated function is a function implemented ona  type rather than an instance of that type

After creating the string we use the io from the standard library by doing `io::stdin()` which returns an instance of io::Stdin which represents
the standard input of our terminal, we then call the read_line method on this instance that will read one line of our standard input, this
method takes one argument which is a variable in which the input will be stored

The `&mut` is using a reference allowing the method to access the variable directly rather than making a copy, the mut is necessary here because
references are also immutable by default

The next part of the code is the `.expect("Failed to read line")` method

the *read_line* call returns io::Result which is an enum that has two variants: *Ok* and *Err*

*expect* is a method on the io::Result instance that checks if the instance is *Ok* or *Err* and will return the return value of the orginal method or
display an error that we pass into expect respectively

In the next and final line we make use of println! placeholders the set of `{}` and list the variables in order of replacement as other arguments to
the method

For the second part of this program we will generate a random number for this we will make use of a crate as a dependency

In our *cargo.toml* under the dependencies section we will add a new dependency:
```
[dependencies]
rand="0.5.5"
```
We specify the name and version of the dependency we want to use

When we cargo build now it will download and compile our dependency before compiling the project now containing these dependencies

This will also generate a *cargo.lock* file that will lock dependencies to a certain version to use specific dependency versions

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

In the first part of this code we are bringing another type into the scope of our code: *Ordering* it is an enum just like *Result* and it
has 3 variants: *Lesser*, *Equal* and *Greater*

We then use a match statement on an expression, the expression is quite simple we are calling the *cmp* method on *guess* which is of type number
and we are comparing *guess* to our *secret_number*, this method returns an *Ordering* enum

The match statement is somewhat like a switch statement in other languages, the way it works is that it will evaluate the expression next to it
then it will go through the *arms* in order and if the evaluated expression matches the *pattern* of that *arm* it will run the code contained in
that *arm*

If we try to run this code it will not compile, the reason for this is that we are trying to compare a string to a number and rust has a strong
typing system

To fix this issue we must add this code to our program:
```
let guess: u32 = guess.trim().parse().expect("Please type a number!");
```

Rust allows us to shadow variables so we use this normally when we want to change the type of a variable, this is what we are doing here

We are calling the *trim* method on the guess string and what this will do is get rid of all whitespace at the beginning and end of the string as well
as newlines

the *parse* method allows us to turn the string into a number, we specify the type of the number on the left side of the expression by using
`: u32` we specify that *guess* is supposed to be a 32-bit unsigned integer, this is called a type annotation,
also the compiler will now assume that *secret_number* is going to be of type u32, *parse* also returns a *Result* enum so we will
need to call *expect* afterwards to handle errors


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

We know that our *parse* method returns a *Result* so we are matching that *Result* to see whether it returns *Ok* or *Err*, if it's ok that *Ok* enum will
contain data corresponding to the result of the method so we are going to return that data, the *Err* will contian information about the error and we
are using a catch-all to say that no matter what data is contained in that enum if it is an error perform a *continue* and start the loop over

The last step is to clean up and stop printing the secret number and the program is complete

# Chapter 3
## 3.1
You can declare a variable like this: `let x = 5;`, they are immutable by default

To make a variable mutable we must add `mut` to its declaration

We can declare constants using `const` instead of `let`

We can also use variable shadowing by repeating the let keyword, shadowing is useful if we want to use the same variable name for a variable of a different
name

We cannot use variable shadowing if a variable is declared as mutable

## 3.2 Data Types
There are two data type subsets: scalar and compound

Scalar type represents a single value, rust has 4 scalar types: integers, floating-point numbers, booleans, and characters

#### Integers
Integers type variants are based on size of the integer, and whether the integer is signed or unsigned, ex: i32(signed 32-bit integer), u64(unsigned 64-bit integer)

possible sizes include: 8, 16, 32, 64, 128

Signed variant can store numbers from -2^(n-1) to 2^(n-1) - 1

Unsigned variant can store numbers from 0 to 2^n - 1

There are also isize and usize variants that depend on the architecture you are using

You can write integer literals in decimal, hex, octal, binary, and byte for u8

#### Floating-point types

Rust has two primitives for floating-point numbers: f32 and f64, the default type is f64

example: 
```
fn main(){
	let x = 2.0; //f64
	
	let y: f32 = 3.0; //f32
}	
```
f32 is a single precision float and f64 has double precision

#### Numeric Operations

Rust supports the basic operations for number types: addition, subtraction, multiplication, division, and remainder

#### Booleans

Booleans are a byte in size, booleans are specified using `bool`

#### Characters

Rust's char type is four bytes in size and represents a Unicode Scalar Value, however characters don't really exist in unicode so they just talk more about this later

#### Tuple

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

#### Array

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

We use the `->` between the parentheses and the squiqly brackets, also notice no `return` keyword, instead there is just no semicolon	at the end of the function

This is because with no semicolon it is an expression and not a statement


## 3.4 Comments
Rust comments are made using the `//` and everything written from the `//` until the end of the line will be commented

## 3.5 Control Flow

#### If statements
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

` let number = if condition { 5 } else { "six" };` 

#### Loops

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


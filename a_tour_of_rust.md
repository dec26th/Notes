# A Tour Of Rust

[TOC]

## 1 Common Language Concepts

### 1.1 Variables and Mutability

- the variables in rust is  default immutable.

```rust
let x = 1;
println!("{}", x);
x = 2;  // error, because x is immutable
println!("{}", x);
```

- if you want to reassign a variable name the same (shadowing)

```rust
let x = 1;
...
let x = 2;

let x = "success";
```

- else

```rust
let mut x = 1;
...
x = 2;
x = "error"; //error!
```

- the difference between variables in rust and the consts.
    - const is always immutable, but variables in rust can be se mutable.
    - the type of const must be annotated while variables can easily declared by the keyword `let`
    - const can be only set as a constant expression, not the result of a function call or any other value that could be computed at runtime.

### 1.2 Data Types

- Four primary scalar types:

    - integers

        - | Length  | Signed | Unsigned |
            | ------- | ------ | -------- |
            | 8-bit   | i8     | u8       |
            | 16-bit  | i16    | u16      |
            | 32-bit  | i32    | u32      |
            | 64-bit  | i64    | u64      |
            | 128-bit | i128   | u128     |
            | arch    | isize  | Size     |

    - floating-point numbers

    - boolens

    - characters

        - 4 Bytes in size

- Compound Type

    - `Tuple`

        - ```rust
            fn main {
            	lex x: (i32, f64, u8) = (500, 6.4, 1);
            	
            	let a = x.0;
            	let b = x.1;
            	let c = x.2;
            }
            ```

    - `Array`

        - ```rust
            let a :[i32; 6] = [1, 2, 3, 4, 5, 6];
            let b = [1, 2, 3, 4, 5, 6];
            let c = [3; 5]; // let c = [3, 3, 3, 3, 3]
            ```

### 1.3 Functions

- Not like C, rust do not care about the position where the function is declared.

- Functions with params

    - ```rust
        fn Hi(a: i32, b:i32) {
        	println!("{}{}", a, b);
        }
        ```

- Functions With Return Values

    - ```rust
        fn five() -> i32 {
        	5  // returns the value implicitly
        }
        ```

### 1.4 Controal Flows

-   There three kinds of loops:

    -   `loop`

        -   run util you explicitly tell it to stop.

        -   you can use loop to return value

            -   ```rust
                let mut counter= 0;
                let result = loop {
                	counter += 1;
                	if counter == 10 {
                		break counter * 2;
                	}
                };
                ```

    -   `while`

    -   `for`

        -   ```rust
            let a = [1, 2,3 ,4 ,5]
            for item in a.iter() {
            	println!("{}", item);
            }
            
            for number in (1..4) {
            	println!("{}", a[number]); // 2, 3, 4
            }
            ```

## 2 Understanding Ownership

### 2.1 Rules Of Ownership

-   Each value in Rust has a variable that's called its owner.  在Rust中，每一个值都有一个变量名为所有者。
-   There can only be  one owner at a time.  永远只有一个所有者
-   When the owner goes out of scope, the value will be dropped. 当所有者不在作用域(out of scope {})时，值将会被丢弃。



-   **Ways Variables and Data Interact: Move**

    -   ```rust
        let x = 5;
        let y = x; // its fine because 5 with a known, fixed size.
        
        let s1 = String::from("hello");  // the struct of String in rust is the same as the slice in Go. With fields length, cap, and a pointer points at the memory where actually stores the value.
        let s2 = s1;
        ```

    -   ![rust_move](./pic/rust_move.svg)

    -   if s1 and s2 really point at the same value, it may causes a probelms. As i state below, when the owner is out of the scope, the value will be free, So the picture may double free the same value. So for memory safety, the **ownership of the value** may **moved to s2**, s1 will no longer be the owner of the value.

-   **Ways Variables and Data Interact: Clone**
    -   User method `clone()`to deep copy the value.

        -   ```rust
            let s1 = String::from("hello");
            let s2 = s1.clone();
            ```

-   **Ownership and Functions**

    -   ```rust
        fn main() {
            let s = String::from("hello");  // s comes into scope
        
            takes_ownership(s);             // s's value moves into the function...
                                            // ... and so is no longer valid here ⚠️⚠️⚠️
        
            let x = 5;                      // x comes into scope
        
            makes_copy(x);                  // x would move into the function,
                                            // but i32 is Copy, so it’s okay to still
                                            // use x afterward
        
        } // Here, x goes out of scope, then s. But because s's value was moved, nothing
          // special happens.
        
        fn takes_ownership(some_string: String) { // some_string comes into scope
            println!("{}", some_string);
        } // Here, some_string goes out of scope and `drop` is called. The backing
          // memory is freed.
        
        fn makes_copy(some_integer: i32) { // some_integer comes into scope
            println!("{}", some_integer);
        } // Here, some_integer goes out of scope. Nothing special happens.
        
        ```



### 2.2 References and Borrowing

-   Pass the **reference of the value** as the params of the function will not change the ownership of the value, because the reference doesn't own the value.

-   But the reference of the value is default immutable

    -   ```rust
        fn main {
        	let s = String::from("hello");
        	try_change_s(&s);
        }
        
        fn try_change_s(s: &String) {
        	s.put_str("hi");
        }
        ```

-   **Mutable References**

    -   ```rust
        fn main {
        	let s = String::from("hello");
        	try_change_s(&mut s);
        }
        
        fn try_change_s(s: &mut String) {
        	s.put_str("hi");
        }
        ```

    -   But mutable references have one big restriction: you can only create a mutable reference to a particular piece of data in a particular scope.

        -   ```rust
            fn main {
            	let s = String::from("hello");
            	let a = &mut s;
            	let b = &mut s; // can not compile
            }
            ```

    -   There is a problem when creating both mutable references and immutable references.

        -   ```rust
            fn main() {
                let mut s = String::from("hello");
            
                let r1 = &s; // no problem
                let r2 = &s; // no problem
                let r3 = &mut s; // BIG PROBLEM
            
                println!("{}, {}, and {}", r1, r2, r3);
            }
            
            ```

        -   But the code below can compile well

            -   ```rust
                fn main() {
                    let mut s = String::from("hello");
                
                    let r1 = &s; // no problem
                    let r2 = &s; // no problem
                    println!("{} and {}", r1, r2);
                    // r1 and r2 are no longer used after this point
                
                    let r3 = &mut s; // no problem
                    println!("{}", r3);
                }
                ```

    -   Rust will prevent the dangling references

        -   ```rust
            fn dangle() -> &String { // dangle returns a reference to a String
            
                let s = String::from("hello"); // s is a new String
            
                &s // we return a reference to the String, s
            } // Here, s goes out of scope, and is dropped. Its memory goes away.
              // Danger!
            ```

### 2.3 Slice Type

-   String Slice is a **referecen** of part of a `String`



## 3 Using Structs to Structure Related Data

### Define a struct with keyword `struct`

-   ```rust
    struct User {
        username: String,
        email: String,
        sign_in_count: u64,
        active: bool,
    }
    ```

### Create a strcut (mutable)

-   ```rust
    fn main() {
        let mut user1 = User {
            email: String::from("someone@example.com"),
            username: String::from("someusername123"),
            active: true,
            sign_in_count: 1,
        };
    
        user1.email = String::from("anotheremail@example.com");
    }
    
    ```

### Using the Field Init Shortened when Variables and Fields Have the Same Value

-   ```rust
    fn build_user(email: String, username: String) -> User {
        User {
            email,
            username,
            active: true,
            sign_in_count: 1,
        }
    }
    ```

### Tuple Struct

-   ```rust
    struct Color(i32, i32, i32);
    ```

### Defining Methods（the first param is always self)

-   ```rust
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

### Associated Functions(Don't take self as the first param)  `String::from()`

-   ```rust
    #[derive(Debug)]
    struct Rectangle {
        width: u32,
        height: u32,
    }
    
    impl Rectangle {
        fn square(size: u32) -> Rectangle {
            Rectangle {
                width: size,
                height: size,
            }
        }
    }
    
    fn main() {
        let sq = Rectangle::square(3);
    }
    
    ```



## 4 Enums and Pattern Matching

### 4.1 Defining an Enum

  -    Define the enum

         -    ```rust
              enum IpAddrKind {
                  V4,
                  V6,
              }
              
              fn main() {
                  let four = IpAddrKind::V4;  // use sytanx :: to use enum
                  let six = IpAddrKind::V6;
              
                  route(IpAddrKind::V4);
                  route(IpAddrKind::V6);
              }
              
              fn route(ip_kind: IpAddrKind) {}
              
              ```

-  Enum with Data

    -  ```rust
            enum IpAddr {
                V4(String),
                V6(String),
            }
        
            let home = IpAddr::V4(String::from("127.0.0.1"));
        
            let loopback = IpAddr::V6(String::from("::1"));
        ```

    -  ```rust
           enum IpAddr {
                V4(u8, u8, u8, u8),
                V6(String),
            }
        
            let home = IpAddr::V4(127, 0, 0, 1);
        
            let loopback = IpAddr::V6(String::from("::1"));
       ```

    -  ```rust
        
        #![allow(unused)]
        fn main() {
        struct Ipv4Addr {
            // --snip--
        }
        
        struct Ipv6Addr {
            // --snip--
        }
        
        enum IpAddr {
            V4(Ipv4Addr),
            V6(Ipv6Addr),
        }
        }
        ```

    -  ```rust
        enum Message { 
            Quit,
            Move { x: i32, y: i32 },
            Write(String),
            ChangeColor(i32, i32, i32),
        }
        ```

-  **The Option Enum and its Advantages Over Null Values**

    -  ```rust
        enum Option<T> {
        	Some(T),
        	None,
        }
        ```

    -  ```rust
        fn main() {
            fn plus_one(x: Option<i32>) -> Option<i32> {
                match x {
                    None => None,
                    Some(i) => Some(i + 1),
                }
            }
        
            let five = Some(5);
            let six = plus_one(five);
            let none = plus_one(None);
        }
        ```

        

### 4.2 The match Control Flow Opertor

-   ```rust
    enum Coin {
        Penny,
        Nickel,
        Dime,
        Quarter,
    }
    
    fn value_in_cents(coin: Coin) -> u8 {
        match coin {
            Coin::Penny => 1,
            Coin::Nickel => 5,
            Coin::Dime => 10,
            Coin::Quarter => 25,
        }
    }
    
    fn main() {}
    
    ```

-   Use `_` as default

    -   ```rust
        fn main() {
            let some_u8_value = 0u8;
            match some_u8_value {
                1 => println!("one"),
                3 => println!("three"),
                5 => println!("five"),
                7 => println!("seven"),
                _ => (),
            }
        }
        ```



## 5 Managing Growing Projects with Projects, Crates, and Modules

### 	5.1 Crates

### 	5.2 Packages

### 	5.3 Modules

## 6 Common Collections

### 6.1 Storing Lists of Values with Vectors

   -    **Creating a New Empty Vector**

           -    ```rust
                let v: Vec<i32> = Vec::new();
                ```

-  **Creating a New Vector Containing values**

    -  ```rust
        let v = vec![1, 2, 3];
        ```

-  **Updating a Vector**

    -  ```rust
        let mut v = Vec::new();
        
        v.push(5);
        v.push(6);
        v.push(7);
        v.push(8);
        ```

-  **Get Ele in Vector**

    -  ```rust
        fn main() {
            let v = vec![1, 2, 3, 4, 5];
        
            let third: &i32 = &v[2]; // return the references 
            println!("The third element is {}", third);
        
            match v.get(2) {  // return the Options<&T>
                Some(third) => println!("The third element is {}", third),
                None => println!("There is no third element."),
            }
        }
        ```

-  **Can not Put Ele in Vector When holding a reference on Vector**

    -  ```rust
        fn main() {
            let mut v = vec![1, 2, 3, 4, 5];
        
            let first = &v[0];
        
            v.push(6);
        
            println!("The first element is: {}", first);
        }
        ```

    -  Because add one more ele in Vector may lead to reallocating the space, `first` may then be pointed to deallocated memory.

-  **Iterating over the Values in a Vector**

    -  ```rust
        fn main() {
            let v = vec![100, 32, 57];
            for i in &v {
                println!("{}", i);
            }
        }
        ```

-  **Using an Enum to Store Multiple Types**

    -  ```rust
        fn main() {
            enum SpreadsheetCell {
                Int(i32),
                Float(f64),
                Text(String),
            }
        
            let row = vec![
                SpreadsheetCell::Int(3),
                SpreadsheetCell::Text(String::from("blue")),
                SpreadsheetCell::Float(10.12),
            ];
        }
        ```

    

### 6.2 Storing UTF-8 Encoded Text with Strings

 -    **Creating a New String**

       -    ```rust
            let mut s = String::new();
            
            let data = "hello";
            let s = data.to_string(); // get the String type from string
            
            let s = String::from("hello");
            ```

- 	**Updateing a String**

    - 	```rust
        fn main() {
            let mut s1 = String::from("foo");
            let s2 = "bar";
            s1.push_str(s2);
            println!("s2 is {}", s2);
          
          
          	s1.put(';'); // the method put can only add a single character.
        }
        ```

- 	**Concatenation with the + Operation of the `format!` Marco**

    - 	```rust
        fn main() {
            let s1 = String::from("Hello, ");
            let s2 = String::from("world!");
            let s3 = s1 + &s2; // note s1 has been moved here and can no longer be used
        }
        ```

    - 	`+` opertor use the `add`method like

        - 	```rust
            fn add(self, s: &str) -> String {}
            ```

    - 	Rust coerce the &String(in this case, &s2) into &str(in this case, &s2[..])

    - 	```rust
        fn main() {
            let s1 = String::from("tic");
            let s2 = String::from("tac");
            let s3 = String::from("toe");
        
            let s = format!("{}-{}-{}", s1, s2, s3);  // will not take any ownership of any of its parameters.
        }
        ```

- 	**Indexing into Strings**

    - 	Rust strings don't support indexing.

        - 	```rust
            let s = String::from("hellp");
            let h = s[0]; // error
            ```

    - 	Why?

        - 	`String` is a wrapper over a `Vec<u8>`.
        - 	it's hard for rust to determine the return type: 1. Byte value? 2, character, 3. a grapheme cluster 4 string slice.

    - 	You should be specific in the indexing and indicate that what you want

        - 	```rust
            let hello = String::from("hello");
            let a = &hello[0..1];
            ```

- 	**Methods for Iterating Over Strings**

    - 	```rust
        for c in "你好".chars() {
        	println!({}, c);
        }
        ```

### 6.3 Storing Keys With Associated Values in HashMaps

 -    **Creating a New Hash Map**

       -    ```rust
            fn main() {
                use std::collections::HashMap;
            
                let mut scores = HashMap::new();
            
                scores.insert(String::from("Blue"), 10);
                scores.insert(String::from("Yellow"), 50);
            }
            ```

- **Access Values in a Hash Map**

    - ```rust
        fn main() {
            use std::collections::HashMap;
        
            let mut scores = HashMap::new();
        
            scores.insert(String::from("Blue"), 10);
            scores.insert(String::from("Yellow"), 50);
        
            let team_name = String::from("Blue");
            let score = scores.get(&team_name);
        }
        ```

    - `get` return an `Option<&V>`, if there is no key in the hash map, `get` will return `None`.

    - Iterate over each key/value pair in a hash map.

        - ```rust
            (fn main() {
                use std::collections::HashMap;
            
                let mut scores = HashMap::new();
            
                scores.insert(String::from("Blue"), 10);
                scores.insert(String::from("Yellow"), 50);
            
                for (key, value) in &scores {
                    println!("{}: {}", key, value);
                }
            }
            ```

    - if the key does not exit, put the default result, else update

        - ```rust
            fn main() {
                use std::collections::HashMap;
            
                let mut scores = HashMap::new();
                scores.insert(String::from("Blue"), 10);
            
                scores.entry(String::from("Yellow")).or_insert(50);
                scores.entry(String::from("Blue")).or_insert(50);
            
                println!("{:?}", scores);
            }
            ```



## 7 Error Handling

### 7.1 Unrecoverable Errors with `panic!`

### 7.2 Recoverable Errors with Result

-   **Handling Potential Failure with the `Result` Type**

    -   ```rust
        enum Result<T, E> {
            Ok(T),
            Err(E),
        }
        ```

-   **Matching on Different Errors**

    -   ```rust
        use std::fs::File;
        use std::io::ErrorKind;
        
        fn main() {
            let f = File::open("hello.txt");
        
            let f = match f {
                Ok(file) => file,
                Err(error) => match error.kind() {
                    ErrorKind::NotFound => match File::create("hello.txt") {
                        Ok(fc) => fc,
                        Err(e) => panic!("Problem creating the file: {:?}", e),
                    },
                    other_error => {
                        panic!("Problem opening the file: {:?}", other_error)
                    }
                },
            };
        }
        ```

        

-   **Shortcuts for Panic on Error: `unwrap` and `expect`**

    -   `match` is a little bit verbose

    -   `unwrap`: the function will return the value inside the `Ok`. If the `Result` is the `Err`, `unwrap` will call the `panic!`

        -   ```rust
            use std::fs::File;
            
            fn main() {
                let f = File::open("hello.txt").unwrap();
            }
            ```

    -   `expect`: the same as `unwrap`, but with more readable message.

-   **A Shortcut for Prepagating Errors: the ? Operator**

    -   ```rust
        
        #![allow(unused)]
        fn main() {
        use std::fs::File;
        use std::io;
        use std::io::Read;
        
        fn read_username_from_file() -> Result<String, io::Error> {
            let mut f = File::open("hello.txt")?; // if is Err, return, else continue
            let mut s = String::new();
            f.read_to_string(&mut s)?;
            Ok(s)
        }
        }
        ```

    -   The `?` placed after a `Result` value is defined to work in almost the same way as the `match` expressions we defined th handle the `Result`. 

        -   If the `Result` is `Ok`, the value inside the `Ok` will get returned from this expression, and the program will continue. 
        -   if the `value` is an `Err`, the `Err` will be returned from the whole function as if we had used the `return`.

### 7.3 To `panic!` or Not To `panic!`

-   **Guidelines for Error Handling**
    -   The bad state is not something that’s *expected* to happen occasionally.
    -   Your code after this point needs to rely on not being in this bad state.
    -   There’s not a good way to encode this information in the types you use.



## 8 Generic Types, Traits, and Lifetimes

### 8.1 Generic Data Types

-   **In Function Definitions**

    -   ```rust
        fn largest<T>(list: &[T]) -> &T {
            let mut largest = &list[0];
        
            for item in list {
                if item > largest {
                    largest = item;
                }
            }
        
            largest
        }
        
        fn main() {
            let number_list = vec![34, 50, 25, 100, 65];
        
            let result = largest(&number_list);
            println!("The largest number is {}", result);
        
            let char_list = vec!['y', 'm', 'a', 'q'];
        
            let result = largest(&char_list);
            println!("The largest char is {}", result);
        }
        ```

    -   It can not compile successfully, because the generics need the `trait` to indicate the type is comparable.

-   **In Struct Definitions**

    -   ```rust
        struct Point<T> {
            x: T,
            y: T,
        }
        
        fn main() {
            let integer = Point { x: 5, y: 10 };
            let float = Point { x: 1.0, y: 4.0 };
        }
        ```

-   **In Enum Definitions **

    -   ```rust
        enum Option<T> {
            Some(T),
            None,
        }
        
        enum Result<T, E> {
            Ok(T),
            Err(E),
        }
        ```

-   **In Method Definitions**

    -   ```rust
        struct Point<T> {
            x: T,
            y: T,
        }
        
        impl<T> Point<T> {
            fn x(&self) -> &T {
                &self.x
            }
        }
        
        fn main() {
            let p = Point { x: 5, y: 10 };
        
            println!("p.x = {}", p.x());
        }
        ```

    -   ```rust
        struct Point<T, U> {
            x: T,
            y: U,
        }
        
        impl<T, U> Point<T, U> {
            fn mixup<V, W>(self, other: Point<V, W>) -> Point<T, W> {
                Point {
                    x: self.x,
                    y: other.y,
                }
            }
        }
        
        fn main() {
            let p1 = Point { x: 5, y: 10.4 };
            let p2 = Point { x: "Hello", y: 'c' };
        
            let p3 = p1.mixup(p2);
        
            println!("p3.x = {}, p3.y = {}", p3.x, p3.y);
        }
        ```

        

### 8.2 Traits: Defining Shared Behavior(in an abstract way)(like interface)

-   **Defining a Trait**

    -   ```rust
        
        #![allow(unused)]
        fn main() {
        pub trait Summary {
            fn summarize(&self) -> String;
        }
        }
        ```

-   **Implementing a Trait on a Type**

    -   ```rust
        
        #![allow(unused)]
        fn main() {
        pub trait Summary {
            fn summarize(&self) -> String;
        }
        
        pub struct NewsArticle {
            pub headline: String,
            pub location: String,
            pub author: String,
            pub content: String,
        }
        
        impl Summary for NewsArticle { // implement a trait
            fn summarize(&self) -> String {
                format!("{}, by {} ({})", self.headline, self.author, self.location)
            }
        }
        
        pub struct Tweet {
            pub username: String,
            pub content: String,
            pub reply: bool,
            pub retweet: bool,
        }
        
        impl Summary for Tweet {
            fn summarize(&self) -> String {
                format!("{}: {}", self.username, self.content)
            }
        }
        }
        ```

-   **Default Implementations**

    -   ```rust
        
        #![allow(unused)]
        fn main() {
        pub trait Summary {
            fn summarize(&self) -> String {
                String::from("(Read more...)")
            }
        }
        
        pub struct NewsArticle {
            pub headline: String,
            pub location: String,
            pub author: String,
            pub content: String,
        }
        
        impl Summary for NewsArticle {}
        
        pub struct Tweet {
            pub username: String,
            pub content: String,
            pub reply: bool,
            pub retweet: bool,
        }
        
        impl Summary for Tweet {
            fn summarize(&self) -> String {
                format!("{}: {}", self.username, self.content)
            }
        }
        }
        ```

-   **Traits as Parameters**

    -   ```rust
        pub fn notify(item: &impl Summary) { // need the keyword impl
            println!("Breaking news! {}", item.summarize());
        }
        ```

        

-   **Trait Bound Syntax**

    -   ```rust
        pub fn notify(item1: &impl Summary, item2: &impl Summary) {
        
        pub fn notify<T: Summary>(item1: &T, item2: &T) {
        ```

-   **Multiple Trait Bounds**

    -   ```rust
        pub fn notify(item: &(impl Summary + Display)) {
        
        pub fn notify<T: Summary + Display>(item: &T) {
        ```

-   **With Trait Bounds with `where` Clauses** 

    -   ```rust
        fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {
        
        fn some_function<T, U>(t: &T, u: &U) -> i32
            where T: Display + Clone,
                  U: Clone + Debug
        {
        ```

### 8.3 Validating References with Lifetimes

-   **Preventing Dangling References with Lifetimes**

-   **Generic Lifttimes in Functions**

    -   ```rust
        fn main() {
            let string1 = String::from("abcd");
            let string2 = "xyz";
        
            let result = longest(string1.as_str(), string2);
            println!("The longest string is {}", result);
        }
        
        fn longest(x: &str, y: &str) -> &str { // Rust does not known which are references.
            if x.len() > y.len() {
                x
            } else {
                y
            }
        }
        
        ```

-   **Lifetime Annotation Syntax**

    -   ```rust
        &i32        // a reference
        &'a i32     // a reference with an explicit lifetime
        &'a mut i32 // a mutable reference with an explicit lifetime
        ```

-   **Lifetime Annotations in Function Signatures**

    -   ```rust
        fn main() {
            let string1 = String::from("abcd");
            let string2 = "xyz";
        
            let result = longest(string1.as_str(), string2);
            println!("The longest string is {}", result);
        }
        
        fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
            if x.len() > y.len() {
                x
            } else {
                y
            }
        }
        ```

    -   The constraint indicates all the references in the params and the return values must have the same lifetime.

    -   The returned reference will also be valid for the length of the **smaller** of the lifetimes of `x` or `y`

        -   ```rust
            fn main() {
                let string1 = String::from("long string is long");
                let result;
                {
                    let string2 = String::from("xyz");
                    result = longest(string1.as_str(), string2.as_str());
                }
                println!("The longest string is {}", result); // error, because the lifetime of result is equal to string2
            }
            
            fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
                if x.len() > y.len() {
                    x
                } else {
                    y
                }
            }
            ```

-   **Lifetime Annotations in Struct Definitions**

    -   ```rust
        struct ImportantExcerpt<'a> {
            part: &'a str,
        }
        
        fn main() {
            let novel = String::from("Call me Ishmael. Some years ago...");
            let first_sentence = novel.split('.').next().expect("Could not find a '.'");
            let i = ImportantExcerpt {
                part: first_sentence,
            };
        }
        ```

-   **Lifetime Elision**

    -   **Compiler uses three rules to figure out what lifetimes references have when there aren't explicit annotations**

        -   Each params that is a references **gets its own lifetime parameter**.

        -   If there is exactly **one input lifetime parameter**, that lifetime is assigned to **all output lifetime parameters**.:`fn foo<'a>(x: &'a i32) -> &'a i32`
        -   If there are **multiple input lifetime parameters**, but one of them is `&self` or `&mut self` because **this is a method**, the lifetime of `self` is assigned to **all output lifetime parameters**.
        -    [References](https://doc.rust-lang.org/book/ch10-03-lifetime-syntax.html)

-   **Lifetime Annotations in Method Definitions**

    -   Refet to the rule three

-   **The Static Lifetime**

    -   `'static` means that this references can live for the entire duration of the program.

-   **Generic Type Parameters, Trait Bounds, and Lifetimes Together**

    -   ```rust
        fn main() {
            let string1 = String::from("abcd");
            let string2 = "xyz";
        
            let result = longest_with_an_announcement(
                string1.as_str(),
                string2,
                "Today is someone's birthday!",
            );
            println!("The longest string is {}", result);
        }
        
        use std::fmt::Display;
        
        fn longest_with_an_announcement<'a, T>(
            x: &'a str,
            y: &'a str,
            ann: T,
        ) -> &'a str
        where
            T: Display,
        {
            println!("Announcement! {}", ann);
            if x.len() > y.len() {
                x
            } else {
                y
            }
        }
        ```



## 9 Writing Automated Tests

### 9.1 How to Write Tests

-   **Three Actions performed by test functions**

    -   Set up any needed data or state
    -   Run the code you want to test
    -   Assert the results are what you expect

-   **The Anatomy of a Test Function**

    -   Add `#[test]` on the line before `fn`, Run Your tests with the `cargo test` command.

    -   ```rust
        #[cfg(test)]
        mod tests {
            #[test]
            fn it_works() {
                assert_eq!(2 + 2, 4);
            }
        
            #[test]
            fn hello_world() {
                println!("Hello, world!");
            }
        
            #[test]
            fn panic_world() {
                panic!("World!");
            }
        }
        ```

-   **Checking Results with the `assert!` Macro**

    -   ```rust
        #[derive(Debug)]
        struct Rectangle {
            width: u32,
            height: u32,
        }
        
        impl Rectangle {
            fn can_hold(&self, other: &Rectangle) -> bool {
                self.width > other.width && self.height > other.height
            }
        }
        
        #[cfg(test)]
        mod tests {
            use super::*;
        
            #[test]
            fn larger_can_hold_smaller() {
                let larger = Rectangle {
                    width: 8,
                    height: 7,
                };
                let smaller = Rectangle {
                    width: 5,
                    height: 1,
                };
        
                assert!(larger.can_hold(&smaller));
            }
        }
        
        fn main() {}
        ```

-   **Testing Equality with the `aeeert_eq!`and `aeesrt_ne!` Macros** 

-   **Adding Custom Failure Messages**

    -   ```rust
        assert!(
                    result.contains("Carol"),
                    "Greeting did not contain name, value was `{}`",
                    result
                );
        ```

-   **Checking for Panics with `should_panic`**

    -   ```rust
        #[test]
        #[should_panic]
        ```

-   **Using `Result<T, E> `in Tests** 

    -   ```rust
        #![allow(unused_variables)]
        fn main() {}
        
        #[cfg(test)]
        mod tests {
            #[test]
            fn it_works() -> Result<(), String> {
                if 2 + 2 == 4 {
                    Ok(())
                } else {
                    Err(String::from("two plus two does not equal four"))
                }
            }
        }
        ```

        

### 9.2 Controlling How Tests Are Run

 - **Running Tests in Parallel or Consecutively**
     - Consecutively
         - `cargo test -- --test-threads=1`
- **Showing Function Output**
    - `cargo test -- --show-output`
- **Running a Subset of Tests by Name**
    - **Run single test**
        - `cargo test {function_name}`
    - **Filtering to Run Multiple Tests**
        - `cargo test add` run function whose name contains add
- **Ignoring Some Tests Unless Specifically Requested**
    - Afert`#[test]` add the tag `#[ignore]`

### 9.3 Test Organization

 - **The Tests Module and `#[cfg(test)]`**
    	- The `#[cfg(test)]` annotation on the tests module tells Rust to compile and run the test code only when you run `cargo test `



## 10 Functional Language Features: Iterators and Closures

### 10.1 Closures: Anonymous Functions that Can Capture Their Environment

 -    **Closures in Rust**

       -    ```rust
            let expensive_closure = |num| {
            	println!("calculating slowly...");
            	thread::sleep(Duration::from_secs(2));
            	num
            };
            ```

      - To define a closure, we start with a pair of vertical pipes `(|)`, inside which we specify the parameters to the closure.

      - ```rust
          use std::thread;
          use std::time::Duration;
          
          fn generate_workout(intensity: u32, random_number: u32) {
              let expensive_closure = |num| {
                  println!("calculating slowly...");
                  thread::sleep(Duration::from_secs(2));
                  num
              };
          
              if intensity < 25 {
                  println!("Today, do {} pushups!", expensive_closure(intensity));
                  println!("Next, do {} situps!", expensive_closure(intensity));
              } else {
                  if random_number == 3 {
                      println!("Take a break today! Remember to stay hydrated!");
                  } else {
                      println!(
                          "Today, run for {} minutes!",
                          expensive_closure(intensity)
                      );
                  }
              }
          }
          
          fn main() {
              let simulated_user_specified_value = 10;
              let simulated_random_number = 7;
          
              generate_workout(simulated_user_specified_value, simulated_random_number);
          }
          ```

- **Closure Type Inference and Annotation**

    - ```rust
        fn  add_one_v1   (x: u32) -> u32 { x + 1 }
        let add_one_v2 = |x: u32| -> u32 { x + 1 };
        let add_one_v3 = |x|             { x + 1 };
        let add_one_v4 = |x|               x + 1  ;
        ```

- **Storing Closeres Using Generic Parameters and the `Fn` Traits**

    - ```rust
        struct Cacher<T>
        where
            T: Fn(u32) -> u32,
        {
            calculation: T,
            value: Option<u32>,
        }
        
        fn main() {}
        ```

    - The `Cacher` struct has a `calculation` field of the generic type `T`. The trait bounds on `T` specify that it's a closure by using the `Fn` trait.

    - ```rust
        struct Cacher<T>
        where
            T: Fn(u32) -> u32,
        {
            calculation: T,
            value: Option<u32>,
        }
        
        impl<T> Cacher<T>
        where
            T: Fn(u32) -> u32,
        {
            fn new(calculation: T) -> Cacher<T> {
                Cacher {
                    calculation,
                    value: None,
                }
            }
        
            fn value(&mut self, arg: u32) -> u32 {
                match self.value {
                    Some(v) => v,
                    None => {
                        let v = (self.calculation)(arg);
                        self.value = Some(v);
                        v
                    }
                }
            }
        }
        
        fn main() {}
        ```

- **Capturing the Enviroment with Closures**

    - There three `Fn` traites as follows:

        - `FnOnce` consumes the variables it captures from its enclosing scope, known as the closure’s *environment*. To consume the captured variables, the closure must take ownership of these variables and move them into the closure when it is defined. The `Once` part of the name represents the fact that the closure **can’t take ownership of the same variables** more than once, so it can be called only once.
        - `FnMut` can change the environment because it **mutably borrows values**.
        - `Fn` borrows values from the environment immutably.

    - Use the keyword `move` before the parameter list to force the closure to take ownership of the values it useds in the environment.

        - ```rust
            fn main() {
                let x = vec![1, 2, 3];
            
                let equal_to_x = move |z| z == x;
            
                println!("can't use x here: {:?}", x); // the value of x is moved into cloures equal_to_x
            
                let y = vec![1, 2, 3];
            
                assert!(equal_to_x(y));
            }
            ```

            

### 10.2 Processing a Series of Items with Iterators

-   **The `Interator` Trait and the `next` Method**

    -   ```rust
        #![allow(unused)]
        fn main() {
        pub trait Iterator {
            type Item;
        
            fn next(&mut self) -> Option<Self::Item>;
        
            // methods with default implementations elided
        }
        }
        ```

          

 -    **Methods that produce Other Iterators**

       -    ```rust
            fn main() {
                let v1: Vec<i32> = vec![1, 2, 3];
            
                v1.iter().map(|x| x + 1); // iterator adaptors are lazy, and we need to consume the iterator here.
            }
            ```

      - Instead, use method  `collect`,

          - ```rust
              fn main() {
                  let v1: Vec<i32> = vec![1, 2, 3];
              
                  let v2: Vec<_> = v1.iter().map(|x| x + 1).collect();
              
                  assert_eq!(v2, vec![2, 3, 4]);
              }
              ```

- **Using Closures that Capture Their Environment**

    - ```rust
        struct Shoe {
            size: u32,
            style: String,
        }
        
        fn shoes_in_my_size(shoes: Vec<Shoe>, shoe_size: u32) -> Vec<Shoe> {
            shoes.into_iter().filter(|s| s.size == shoe_size).collect()
        }
        ```

- **Creating Our Own Iterators with the `Iterator` Trait**

    - ```rust
        struct Counter {
            count: u32,
        }
        
        impl Counter {
            fn new() -> Counter {
                Counter { count: 0 }
            }
        }
        
        impl Iterator for Counter {
            type Item = u32;
        
            fn next(&mut self) -> Option<Self::Item> {
                if self.count < 5 {
                    self.count += 1;
                    Some(self.count)
                } else {
                    None
                }
            }
        }
        
        fn main() {}
        ```

        

### 10.3 Improving Our I/O Project

 - **Removing a `clone` Using an Iterator**

### 10.4 Comparing Performance: Loops vs. Iterators

-   Iterators is better than Loops, slightlt faster.





## 11 Smart Pointers

### 11.1 Using Box to Point to Data on the Heap

-   `Box<T>`,  type that point to Data on the Heap.

-    **Using a `Box<T>` to Store Data on the Heap**

    -   ```rust
        fn main() {
            let b = Box::new(5);
            println!("b = {}", b);
        }
        ```

-   **Enabling Recursive Types with Boxes**

    -   Recursive Type: Whose size can't be known at compile time.

-   **More Information About the Cons List**

    -   Using the `List` type to store the list `1, 2, 3` would look like the code

        -   ```rust
            enum List {
                Cons(i32, List),
                Nil,
            }
            
            use crate::List::{Cons, Nil};
            
            fn main() {
                let list = Cons(1, Cons(2, Cons(3, Nil)));
            }
            ```

        -   **the code will not compile successfully⚠️**

-   **Computing the Size of a Non-Recursive Type**

    -   In the enums `Message`. Rust will **go through each of the variants** to see which variant **needs the most space**.

        -   ```rust
            enum Message {
                Quit,
                Move { x: i32, y: i32 },
                Write(String),
                ChangeColor(i32, i32, i32),
            }
            
            fn main() {}
            ```

-   **Using `Box<T>` to Get a Recursive Type with a Known Size**

    -   Change the data struct to store data indirectly by storing a pointer to the value instead. Because the size of pointer is always ensured.

    -   So we can chagne the code

        -   ```rust
            enum List {
                Cons(i32, Box<List>),
                Nil,
            }
            
            use crate::List::{Cons, Nil};
            
            fn main() {
                let list = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil))))));
            }
            ```

            

### 11.2 Treating Smart Pointers Like Regular References with the Deref Trait

-   **Implementing the `Deref` trait allows you to customize the behavior of the dereference oprator `*`** 

    -   ```rust
        fn main() {
            let x = 5;
            let y = &x;
        
            assert_eq!(5, x);
            assert_eq!(5, *y);
        }
        ```

-   **Using `Box<T>` Like a REference**

    -   ```rust
        fn main() {
            let x = 5;
            let y = Box::new(x);
        
            assert_eq!(5, x);
            assert_eq!(5, *y);
        }
        ```

-   **Defining Out Own Smart Pointer**

    -   ```rust
        struct MyBox<T>(T); // is a tuple struct
        
        impl<T> MyBox<T> {
            fn new(x: T) -> MyBox<T> {
                MyBox(x)
            }
        }
        
        fn main() {}
        ```

-   **Treating a Type Like a Reference by Implementing the `Deref` Trait**

    -   ```rust
        use std::ops::Deref;
        
        impl<T> Deref for MyBox<T> {
            type Target = T;
        
            fn deref(&self) -> &T {
                &self.0
            }
        }
        
        struct MyBox<T>(T);
        
        impl<T> MyBox<T> {
            fn new(x: T) -> MyBox<T> {
                MyBox(x)
            }
        }
        
        fn main() {
            let x = 5;
            let y = MyBox::new(x);
        
            assert_eq!(5, x);
            assert_eq!(5, *y);
        }
        ```

### 11.3 Running Code on Cleanup with the Drop Trait

-   **The smart pattern is `Drop`, which letes you customize what happens when a value is about to go out of scope.**	

    -   ```rust
        struct CustomSmartPointer {
            data: String,
        }
        
        impl Drop for CustomSmartPointer {
            fn drop(&mut self) {
                println!("Dropping CustomSmartPointer with data `{}`!", self.data);
            }
        }
        
        fn main() {
            let c = CustomSmartPointer {
                data: String::from("my stuff"),
            };
            let d = CustomSmartPointer {
                data: String::from("other stuff"),
            };
            println!("CustomSmartPointers created.");
        }
        ```

-   **Dropping a Value Early with `std::mem::drop`**
    
    -   Can not call the `Drop` trait's `drop` manually. Instead, you have to call the `std::mem::drop` function provided by the standard library.

### 11.4 Rc, the Reference Counted Smart Pointer

-   **Using `Rc<T>` to Share Data**

    -   ```rust
        enum List {
            Cons(i32, Box<List>),
            Nil,
        }
        
        use crate::List::{Cons, Nil};
        
        fn main() {
            let a = Cons(5, Box::new(Cons(10, Box::new(Nil))));
            let b = Cons(3, Box::new(a)); 
            let c = Cons(4, Box::new(a)); // the ownership of a shifted, compile error
        }
        ```

    -   ```rust
        enum List {
            Cons(i32, Rc<List>),
            Nil,
        }
        
        use crate::List::{Cons, Nil};
        use std::rc::Rc;
        
        fn main() {
            let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
            let b = Cons(3, Rc::clone(&a));
            let c = Cons(4, Rc::clone(&a));
        }
        ```

    -   Use `Rc::strong_count()` to count the number of reference.

### 11.5 RefCell and the interior Mutability Pattern(Todo)

-   **Enforcing Borrowing Rules at Runtime with `RefCell<T>`**
    -   

### 11.6 Reference Cycles Can Leak Memory(Todo)



## 12 Fearless Concurrency

### 12.1 Using Threads to Run Code SImultaneously

-   **Creating a New Thread with `spawn`**

    -   ```rust
        use std::thread;
        use std::time::Duration;
        
        fn main() {
            thread::spawn(|| {
                for i in 1..10 {
                    println!("hi number {} from the spawned thread!", i);
                    thread::sleep(Duration::from_millis(1));
                }
            });
        
            for i in 1..5 {
                println!("hi number {} from the main thread!", i);
                thread::sleep(Duration::from_millis(1));
            }
        }
        ```

-   **Waiting for All Threads to Finish Using `join` Handles**

    -   ```rust
        use std::thread;
        use std::time::Duration;
        
        fn main() {
            let handle = thread::spawn(|| {
                for i in 1..10 {
                    println!("hi number {} from the spawned thread!", i);
                    thread::sleep(Duration::from_millis(1));
                }
            });
        
            for i in 1..5 {
                println!("hi number {} from the main thread!", i);
                thread::sleep(Duration::from_millis(1));
            }
        
            handle.join().unwrap();
        }
        ```

         

-   **Using `move` Closures with Threads**

### 12.2 Using Message Passing to Transfer Data Between Threads

-   **A channel in programming has two halves: Transmitter, receiver**

    -   ```rust
        use std::sync::mpsc;
        
        fn main() {
            let (tx, rx) = mpsc::channel(); // one send and another receive
          // mpsc stands for multiple producer, single consumer.
        }
        ```

    -   ```rust
        use std::sync::mpsc;
        use std::thread;
        
        fn main() {
            let (tx, rx) = mpsc::channel();
        
            thread::spawn(move || {
                let val = String::from("hi");
                tx.send(val).unwrap();
            });
        }
        ```

    -   The `send` method return a `Result<T,E>` type.If there is nowher to send a value, it will return an error, and the `unwrap()`will `panic`

    -   ```rust
        use std::sync::mpsc;
        use std::thread;
        
        fn main() {
            let (tx, rx) = mpsc::channel();
        
            thread::spawn(move || {
                let val = String::from("hi");
                tx.send(val).unwrap();
            });
        
            let received = rx.recv().unwrap();
            println!("Got: {}", received);
        }
        ```

    -   `recv` block until it send.

    -   `try_recv` doesn't block， but still return `Result<T,E>` immediately. an `Ok` value holding a message if one is avaible and an `Err` value if there aren't any messages this time.

-   **Channels and Ownership Transference**

    -   The `send` function takes ownership of its parameter, when the value is moved, the receiver takes ownership of it.

-   **Treat `tx` as an iterator**

    -   ```rust
        for received in rx { // stopped when the channel is closed
        	...
        }
        ```

-   **Creating Multiple Producers by Cloning the Transmitter**

    -   ```rust
        let (tx, rx) = mpsc::channel();
        let tx1 = mpsc::Sender::Clone(&tx);
        ```

        

### 12.3 Shared-State Concurrency

-   **Using Mutexes to Allow Access to Data from One Thread at a Time**

    -   The API of `Mutex<T>`

        -   ```rust
            use std::sync::Mutex;
            
            fn main() {
                let m = Mutex::new(5);
            
                {
                    let mut num = m.lock().unwrap();
                    *num = 6;
                }
            
                println!("m = {:?}", m);
            }
            ```

            

### 12.4 Extensible Concurrency with the Sync and Send Traits


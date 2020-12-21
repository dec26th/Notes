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
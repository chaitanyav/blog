---
title: "From, Into traits" 
date: 2023-09-07
---

![Rust logo](https://www.rust-lang.org/logos/rust-logo-32x32-blk.png)

Let's say we have a user-defined type `Point` defined as follows

```rust
struct Point {
    x: i32,
    y: i32,
}
```

and we want a `String` or `&str` of the form `"(1,2)"` to be converted into `Point` type. We need to implement
the `From` trait on the `Point` type so that we can do something like

```rust
let p: Point = Point::from(String::from("(1,2)"));
```

or

```rust
let p: Point = Point::from("(1,2)");
```

The rust std library documentation says only to implement `From` 
since `From<T> for U implies Into<U> for T` which means by implementing the `From` trait on
the `Point` type, we can make use of the standard library implementation of the `Into` trait. We can now also do

```rust
let p: Point = String::from("(1,2)").into();
```
or

```rust
let p: Point = "(1,2)".into();
```

Here is an implementation of the `From` trait on the `Point` type.

```rust
impl From<String> for Point {
    fn from(value: String) -> Self {
        let re = Regex::new(r#"\((\d+),(\d+)\)"#).unwrap();
        if let Some(caps) = re.captures(&value) {
            return Point {
                x: i32::from_str_radix(&caps[1], 10).unwrap(),
                y: i32::from_str_radix(&caps[2], 10).unwrap(),
            };
        }
        Point { x: 0, y: 0 }
    }
}

impl From<&str> for Point {
    fn from(value: &str) -> Self {
        let re = Regex::new(r#"\((\d+),(\d+)\)"#).unwrap();
        if let Some(caps) = re.captures(&value) {
            return Point {
                x: i32::from_str_radix(&caps[1], 10).unwrap(),
                y: i32::from_str_radix(&caps[2], 10).unwrap(),
            };
        }
        Point { x: 0, y: 0 }
    }
}
```

***NOTE*** as per the documentation the `From` trait should not fail that is the reason
i chose to return `Point { x: 0, y: 0}` at the end in case the regex does not capture.

Here is the link to the complete program on [Rust Playground](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=5dbcb6b49ac23af447616052be8fb3c2)

### References
1. https://doc.rust-lang.org/std/convert/trait.From.html
2. https://doc.rust-lang.org/std/convert/trait.From.html#tymethod.from

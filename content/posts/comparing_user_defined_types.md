---
title: "How to compare user defined types in Rust" 
date: 2023-08-25
---

![Rust logo](https://www.rust-lang.org/logos/rust-logo-32x32-blk.png)

Let's just say we have a user-defined type Person defined as follows:

```rust

    pub struct Person {
      name: String,
      age: i32
    }

```

We need to compare two Persons `mat`, `mac` defined as follows:

```rust

    let mat: Person = Person{name: String::from("Mat"), age: 30};
    let mac: Person = Person{name: String::from("Mac"), age: 35};

```

We cannot just do `mat == mac` or `mat != mac`. We have to define what equality means 
for the type Person. We can do this in two ways.

1. Deriving the PartialEq trait for Person

```rust

    #[derive(PartialEq)]
    pub struct Person {
      name: String,
      age: i32
    }

```

By deriving the `PartialEq` trait, the compiler implements the equality operator on the Person type. When we do `mat == mac`, the compiler compares each field of `mat` with the corresponding field of `mat` i.e. `mat.age == mac.age` and `mat.name == mac.name`.

```rust

    println!("Is mat == mac ? {}", mat == mac); 

```

2. Implementing the PartialEq trait for Person

We can implement the `PartialEq` trait for Person ourselves. For that, we have to implement the required method `eq` of the trait `PartialEq`.

```rust

    impl PartialEq for Person {
      fn eq(&self, other: &Self) -> bool {
        self.name == other.name && self.age == other.age
      }
    }

```

Now, lets say if we want to know if `mac` is older or younger than `mat` i.e. `mat > mac` or `mat < mac` or `mat <= mac` or `mat >= mac`. We would have to implement the `Ord`, `PartialOrd` and `Eq` traits for the Person type. These can be implemented as follows:

```rust

    impl PartialOrd for Person {
      fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        Some(self.cmp(other))
      }
    }

    impl Ord for Person {
      fn cmp(&self, other: &Self) -> Ordering {
        self.age.cmp(&other.age)
      }
    }

    impl PartialEq for Person {
      fn eq(&self, other: &Self) -> bool {
        self.age == other.age
      }
    }

    impl Eq for Person {}

```

Note: The `Eq` trait inherits from the `PartialEq` trait.

Now when we do

```rust

    println!("Is mac older than mat ? {}", mac > mat);

```

we get

```rust

    Is mac older than mat ? true

```

### References
1. https://doc.rust-lang.org/std/cmp/trait.PartialEq.html
2. https://doc.rust-lang.org/std/cmp/trait.Eq.html 
3. https://doc.rust-lang.org/std/cmp/trait.Ord.html
4. https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html

---
title: "Iterators on user-defined types, Part-1" 
date: 2023-09-03
---

![Rust logo](https://www.rust-lang.org/logos/rust-logo-32x32-blk.png)

Let's say we have a user-defined type `Dictionary` which is defined as follows:

```rust
struct Dictionary {
    words: Vec<String>,
}

impl Dictionary {
    fn new() -> Self {
        Dictionary { words: Vec::new() }
    }

    fn insert(&mut self, val: String) {
        self.words.push(val);
    }
}
```

We would like to implement an iterator on our `Dictionary` type so that we can loop over all the words in the dictionary like

```rust
let mut dict = Dictionary::new();
dict.insert(String::from("abashed"));
dict.insert(String::from("abashings"));
dict.insert(String::from("abashment"));
dict.insert(String::from("abate"));

for word in dict {
  println!("{}", word);
}
```

Rust provides us with the `Iterator` trait which can be used to implement iterator on user-defined types. But the loop in the above code snippet, actually converts the `dict` into an iterator. The two for loops in the code snippet below are equivalent

```rust
for word in dict.into_iter() {
  println!("{}", word);
}

for word in dict {
  println!("{}", word);
}
```

So we need to implement the `IntoIterator` trait on our `Dictionary` type, which can be implemented as follows:

```rust
impl IntoIterator for Dictionary {
    type Item = String;
    type IntoIter = <Vec<Self::Item> as IntoIterator>::IntoIter;

    fn into_iter(self) -> Self::IntoIter {
        self.words.into_iter()
    }
}
```
where `Item` is the type of the item the iterator is going to return and `IntoIter` is the kind of iterator we want our `Dictionary` type to be converted into. Here we want it to be converted into an iterator of `Vec<String>` (which already implements the `IntoIterator` trait). Rust playground link for the above [program](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=f2ceb5478f77adcc9ba65f112e51bf15).

***NOTE***: The `into_iter()` consumes(moves) the `Vec<String>`, notice the `self` as the argument for the `into_iter` function. Any subsequent use of the iterator is not allowed by the compiler like

```rust
for word in dict.into_iter() {
  println!("{}", word);
}

for word in dict {
  println!("{}", word);
}
```

is not allowed by the compiler.

```rust
...
33 |     for word in dict {
   |                 ---- `dict` moved due to this implicit call to `.into_iter()`
...
41 |     for word in dict.into_iter() {
   |                 ^^^^ value used here after move
   |
note: `into_iter` takes ownership of the receiver `self`, which moves `dict`
  --> /rustc/5680fa18feaa87f3ff04063800aec256c3d4b4be/library/core/src/iter/traits/collect.rs:271:18
```

The `IntoIterator` implementation can also be done in a different way. I took this idea from the answer to this stack overflow [question](https://stackoverflow.com/questions/30218886/how-to-implement-iterator-and-intoiterator-for-a-simple-struct?rq=3). Rust playground link to the [program](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=a526792db0ed410bb33f82e6e45e91a9).

### References:
1. https://doc.rust-lang.org/std/iter/trait.IntoIterator.html
2. https://doc.rust-lang.org/std/iter/trait.IntoIterator.html#associatedtype.IntoIter
3. https://doc.rust-lang.org/std/iter/index.html#implementing-iterator
4. https://stackoverflow.com/questions/30218886/how-to-implement-iterator-and-intoiterator-for-a-simple-struct?rq=3

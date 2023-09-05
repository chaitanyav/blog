---
title: "Operator overloading in Rust" 
date: 2023-09-01
---

![Rust logo](https://www.rust-lang.org/logos/rust-logo-32x32-blk.png)

Let's say we have a user-defined type `Rational` defined as follows:

```rust
#[derive(Debug)]
struct Rational {
    num: i32,
    den: i32,
}
```

In order to support arithmetic operators  like (`+`, `-`, `/`, `*`) on `Rational`, we would have to implement the `Add`, `Sub`, `Div`, `Mul` traits on `Rational` from the Rust standard library. To implement the `Add` trait
on `Rational`, we need to provide an implementation for the `add` method  from the `Add` trait.

```rust
impl Add for Rational {
    type Output = Self;
    fn add(self, other: Self) -> Self::Output {
        let temp_lcm: i32 = lcm(self.den, other.den);
        let temp_num: i32 = (temp_lcm / self.den) * self.num + (temp_lcm / other.den) * other.num;

        Rational {
            num: temp_num,
            den: temp_lcm,
        }
    }
}
```
***NOTE***: The above code for addition does not simplify the rational number completely. Also making use of the fact that `lcm(a, b) = (a * b) / gcd(a, b)` to simplify the result of the addition, subtraction a bit.
Similarly, we can also implement the `Sub`, `Div`, `Mul` traits on `Rational` as follows:

```rust
impl Sub for Rational {
    type Output = Self;
    fn sub(self, other: Self) -> Self::Output {
        let temp_lcm: i32 = lcm(self.den, other.den);
        let temp_num: i32 = (temp_lcm / self.den) * self.num - (temp_lcm / other.den) * other.num;
        Rational {
            num: temp_num,
            den: temp_lcm,
        }
    }
}

impl Mul for Rational {
    type Output = Self;
    fn mul(self, other: Self) -> Self::Output {
        Rational {
            num: self.num * other.num,
            den: self.den * other.den,
        }
    }
}

impl Div for Rational {
    type Output = Self;
    fn div(self, other: Self) -> Self::Output {
        Rational {
            num: self.num * other.den,
            den: other.num * self.den,
        }
    }
}
```

Code demonstrating the arithmetic operators on `Rational` objects (Link to the complete program at [Rust Playground](https://play.rust-lang.org/?version=stable&mode=debug&edition=2021&gist=97c46ec0f98f70d3b5298f526b5db179))

```rust
fn main() {
    let a: Rational = Rational { num: 1, den: 8 };
    let b: Rational = Rational { num: 1, den: 3 };
    let c = a + b;
    println!("{}", c);
    
    let a: Rational = Rational { num: 1, den: 8 };
    let b: Rational = Rational { num: 1, den: 3 };
    let c = a - b;
    println!("{}", c);
}

```

The above code produces the following output:
```
11 / 24
-5 / 24
```
***NOTE***: The `Display` trait was also implemented on `Rational` so that it can be displayed on stdout as `num / den`.

```rust
impl Display for Rational {
    fn fmt(&self, f: &mut Formatter<'_>) -> Result<(), Error> {
        write!(f, "{} / {}", self.num, self.den)
    }
}
```
The Rust standard libary also has traits for defining assigning arithmetic operations like `a += b` or `a *= b`. See https://doc.rust-lang.org/std/ops/index.html for supported
operators.


### References:
1. https://doc.rust-lang.org/std/ops/trait.Add.html
2. https://doc.rust-lang.org/std/ops/trait.Sub.html
3. https://doc.rust-lang.org/std/ops/trait.Mul.html
4. https://doc.rust-lang.org/std/ops/trait.Div.html
5. https://doc.rust-lang.org/std/fmt/trait.Display.html

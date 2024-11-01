# 方法

```rust
struct Circle {
    x: f64,
    y: f64,
    radius: f64,
}

impl Circle {
    // new是Circle的关联函数，因为它的第一个参数不是self，且new并不是关键字
    // 这种方法往往用于初始化当前结构体的实例
    fn new(x: f64, y: f64, radius: f64) -> Circle {
        Circle {
            x: x,
            y: y,
            radius: radius,
        }
    }

    // Circle的方法，&self表示借用当前的Circle结构体
    fn area(&self) -> f64 {
        std::f64::consts::PI * (self.radius * self.radius)
    }

    // &mut self 改变结构体实例属性
    fn expand(&mut self, r: f64) {
        self.radius = r
    }
}

fn main() {
    // 在一个 impl 块内，Self 指代被实现方法的结构体类型，self 指代此类型的实例
    // self 表示 Rectangle 的所有权转移到该方法中，这种形式用的较少
    // &self 表示该方法对 Circle 的不可变借用
    // &mut self 表示可变借用
    let mut c = Circle::new(1.0, 2.0, 3.0);
    println!("circle area is {:.2}", c.area()); // circle area is 28.27

    c.expand(6f64);
    println!("new circle area is {:.2}", c.area()) // new circle area is 113.10
}


```

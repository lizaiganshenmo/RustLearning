# 模式匹配

- ### match

  ```rust
  match target {
      模式1 => 表达式1,
      模式2 => {
          语句1;
          语句2;
          表达式2
      },
      _ => 表达式3
  }
  
  
  enum Direction {
      East,
      West,
      North,
      South,
  }
  
  fn main() {
      let dire = Direction::South;
      match dire {
          Direction::East if 2 > 1 => println!("East"), // if 2 > 1 匹配守卫 同时为true ,匹配上
          Direction::North | Direction::South => {
              println!("South or North");
          }
          _ => (),
      };
  
      // 宏：matches!，它可以将一个表达式跟模式进行匹配，然后返回匹配的结果 true or false
      let foo = 'f';
      assert!(matches!(foo, 'A'..='Z' | 'a'..='z')); // true
  
      let bar = Some(4);
      assert!(matches!(bar, Some(x) if x > 2)); // true
  }
  
  ```
  
  
  
- ### If let匹配

  ```rust
  fn main() {
      let v = Some(3u8);
      if let Some(3) = v {
          println!("three");
      }
  }
  
  ```
  
  
  
- ### 匹配option

  ```rust
  fn main() {
      let five = Some(5);
      let six = plus_one(five);
      let none = plus_one(None);
  
      if let Some(six_num) = six {
          println!("six val is {six_num}") // six val is 6
      }
  
      if let Some(t) = none {
          println!("none val is {t}")
      } else {
          println!("get none") // get none
      }
  }
  
  fn plus_one(x: Option<i32>) -> Option<i32> {
      match x {
          None => None,
          Some(i) => Some(i + 1),
      }
  }
  
  ```
  
  
  
- ### let else

  ```rust
  fn main() {
      let five = Some(5);
      let six = plus_one(five);
  
      if let Some(t) = six {
          println!("t is {t}")
      }
      // println!("t is {t}"); // cannot find value `t` in this scope
  
      let Some(six_num) = six else {
          return;
      };
  
      // let-else 的一个显著特点在于其解包成功时所创建的变量具有更广的作用域
      println!("six_num is {six_num}")
  }
  
  fn plus_one(x: Option<i32>) -> Option<i32> {
      match x {
          None => None,
          Some(i) => Some(i + 1),
      }
  }
  
  ```
  
  
  
- ### @绑定

  ```rust
  enum Message {
      Hello { id: i32 },
  }
  #[derive(Debug)]
  struct Point {
      x: i32,
      y: i32,
  }
  
  fn main() {
      // @ 绑定
      // 我们希望测试 Message::Hello 的 id 字段是否位于 3..=7 范围内，同时也希望能将其值绑定到 id_variable 变量中以便此分支中相关的代码可以使用它
      let msg = Message::Hello { id: 5 };
      match msg {
          Message::Hello {
              id: id_variable @ 3..=7,
          } => {
              println!("Found an id in range: {}", id_variable) // Found an id in range: 5
          }
          Message::Hello { id: 10..=12 } => {
              println!("Found an id in another range")
          }
          Message::Hello { id } => {
              println!("Found some other id: {}", id)
          }
      }
  
      // 使用 @ 还可以在绑定新变量的同时，对目标进行解构
      let point = Point { x: 10, y: 5 };
      if let p @ Point { x: 10, y } = point {
          println!("x is 10 and y is {} in {:?}", y, p); // x is 10 and y is 5 in Point { x: 10, y: 5 }
      } else {
          println!("x was not 10 :(");
      }
  
      match 1 {
          num @ (1 | 2) => {
              println!("{}", num); // 1
          }
          _ => {}
      }
  }
  
  ```

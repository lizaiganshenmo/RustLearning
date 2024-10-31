# 流程控制

- ### if

  ```rust
  fn main() {
      let condition = true;
      let number = if condition { 5 } else { 6 };
  
      if number == 5 {
          println!("ahhhhhh")
      }
      println!("The value of number is: {}", number);
  }
  
  ```
  
  
  
- ### for

  | 使用方法                      | 等价使用方式                                      | 所有权     |
  | ----------------------------- | ------------------------------------------------- | ---------- |
  | `for item in collection`      | `for item in IntoIterator::into_iter(collection)` | 转移所有权 |
  | `for item in &collection`     | `for item in collection.iter()`                   | 不可变借用 |
  | `for item in &mut collection` | `for item in collection.iter_mut()`               | 可变借用   |
  
  ```rust
  fn main() {
      // for 元素 in 集合 {
      // 使用元素干一些你懂我不懂的事情
      // }
      for i in 1..=5 {
          if i == 1 {
              continue;
          }
          if i == 5 {
              break;
          }
          println!("{}", i);
      }
  
      let a = [4, 3, 2, 1];
      // `.iter()` 方法把 `a` 数组变成一个迭代器
      for (i, v) in a.iter().enumerate() {
          println!("第{}个元素是{}", i + 1, v);
      }
  }
  
  ```
  
  
  
- ### while

  ```rust
  fn main() {
      let mut n = 0;
  
      while n <= 5  {
          println!("{}!", n);
          n = n + 1;
      }
      println!("我出来了！");
  }
  ```
  
  
  
- ### loop

  ```rust
  fn main() {
      let mut counter = 0;
      
      // loop 是一个表达式，因此可以返回一个值
      let result = loop {
          counter += 1;
  
          if counter == 10 {
              break counter * 2; // break 可以单独使用，也可以带一个返回值，有些类似 return
          }
      };
  
      println!("The result is {}", result);
  }
  ```
  

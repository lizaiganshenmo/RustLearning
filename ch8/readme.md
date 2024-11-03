# 生命周期

- ### 生命周期

  ```rust
  // 生命周期，简而言之就是引用的有效作用域
  // 生命周期的主要作用是避免悬垂引用，它会导致程序引用了本不该引用的数据
  
  fn main() {
      // 函数生命周期demo
      let string1 = String::from("long string is long");
  
      {
          let string2 = String::from("xyz");
          let result = longest(string1.as_str(), string2.as_str());
          println!("The longest string is {}", result); // The longest string is long string is long
      }
  
      // 结构体生命周期Demo
      let novel = String::from("Call me Ishmael. Some years ago...");
      let first_sentence = novel.split('.').next().expect("Could not find a '.'");
      let i = ImportantExcerpt {
          part: first_sentence,
      };
  
      println!("{:?}", i);
  
      // 字符串生命周期小于 i, 编译报错
      // let i;
      // {
      //     let novel = String::from("Call me Ishmael. Some years ago...");
      //     let first_sentence = novel.split('.').next().expect("Could not find a '.'");
      //     i = ImportantExcerpt {
      //         part: first_sentence,
      //     };
      // }
      // println!("{:?}",i);
  
      // 静态生命周期
      //  'static，拥有该生命周期的引用可以和整个程序活得一样久
      let s: &'static str = "我没啥优点，就是活得久，嘿嘿";
  }
  
  #[derive(Debug)]
  // 结构体中的生命周期
  struct ImportantExcerpt<'a> {
      part: &'a str, // 所引用的字符串 str 生命周期需要大于等于该结构体的生命周期
  }
  
  // 方法的生命周期
  impl<'a: 'b, 'b> ImportantExcerpt<'a> {
      // 'a: 'b，是生命周期约束语法，跟泛型约束非常相似，用于说明 'a 必须比 'b 活得久
      fn announce_and_return_part(&'a self, announcement: &'b str) -> &'b str {
          println!("Attention please: {}", announcement);
          self.part
      }
  }
  
  // // 在存在多个引用时，编译器有时会无法自动推导生命周期，此时就需要我们手动去标注，通过为参数标注合适的生命周期来帮助编译器进行借用检查的分析
  //  函数签名中的生命周期标注
  fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
      // x、y 和返回值至少活得和 'a 一样久（因为返回值要么是 x，要么是 y）
      if x.len() > y.len() {
          x
      } else {
          y
      }
  }
  
  fn first_word(s: &str) -> &str {
      // 函数参数生命周期消除规则
      // 每一个引用参数都会获得独自的生命周期
      // 若只有一个输入生命周期（函数参数中只有一个引用类型），那么该生命周期会被赋给所有的输出生命周期
      // 若存在多个输入生命周期，且其中一个是 &self 或 &mut self，则 &self 的生命周期被赋给所有的输出生命周期
  
      let bytes = s.as_bytes();
  
      for (i, &item) in bytes.iter().enumerate() {
          if item == b' ' {
              return &s[0..i];
          }
      }
  
      &s[..]
  }
  
  
  ```
  
  

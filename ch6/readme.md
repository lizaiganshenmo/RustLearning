# 泛型和特征

- ### 泛型

  ```rust
  // 泛型性能
  // 在 Rust 中泛型是零成本的抽象，意味着你在使用泛型时，完全不用担心性能上的问题
  // Rust 是在编译期为泛型对应的多个类型，生成各自的代码，因此损失了编译速度和增大了最终生成文件的大小
  
  fn add<T: std::ops::Add<Output = T>>(a: T, b: T) -> T {
      // 泛型操作 需要实际参数类型 实现对应操作的特征
      a + b
  }
  
  struct Point<T, E> {
      // 结构体泛型
      x: T,
      y: E,
  }
  
  // 方法中使用泛型
  impl<T, E> Point<T, E> {
      fn getX(&self) -> &T {
          &self.x
      }
      fn getY(&self) -> &E {
          &self.y
      }
  }
  
  // 为具体的泛型类型实现方法
  impl Point<i32, i32> {
      fn calArea(&self) -> i32 {
          self.getX() * self.getY()
      }
  }
  
  // 枚举中泛型
  enum Option<T> {
      Some(T),
      None,
  }
  enum Result<T, E> {
      Ok(T),
      Err(E),
  }
  // 常量泛型
  struct Buffer<const N: usize> {
      data: [u8; N],
  }
  
  // 常量函数 const fn 允许我们在编译期对函数进行求值，从而实现更高效、更灵活的代码设计
  const fn compute_buffer_size(factor: usize) -> usize {
      factor * 1024
  }
  
  fn main() {
      println!("add i8: {}", add(2i8, 3i8)); // add i8: 5
      println!("add i32: {}", add(20, 30)); // add i32: 50
      println!("add f64: {}", add(1.23, 1.23)); // add f64: 2.46
  
      let _integer = Point { x: 5, y: 10 };
      let _float = Point { x: 1.0, y: 4.0 };
      println!("_float x is:{} y is:{:.2}", _float.getX(), _float.getY()); // _float x is:1 y is:4.00
  
      println!("_integer area is :{}", _integer.calArea()); // _integer area is :50
  
      // println!("_float area is :{}", _float.calArea()) // no method named `calArea` found for struct `Point<{float}, {float}>` in the current scope
  
      const SIZE: usize = compute_buffer_size(4);
      let buffer = Buffer::<SIZE> { data: [0; SIZE] };
      println!("Buffer size: {} bytes", buffer.data.len()); // Buffer size: 4096 bytes
  }
  
  ```
  
  
  
- ### 特征

  ```rust
  // 特征定义了一组可以被共享的行为，只要实现了特征，你就能使用这组行为
  pub trait Summary {
      fn summarize(&self) -> String;
  }
  pub struct Post {
      pub title: String,   // 标题
      pub author: String,  // 作者
      pub content: String, // 内容
  }
  
  impl Summary for Post {
      fn summarize(&self) -> String {
          format!("文章{}, 作者是{}", self.title, self.author)
      }
  }
  
  pub struct Weibo {
      pub username: String,
      pub content: String,
  }
  
  impl Summary for Weibo {
      fn summarize(&self) -> String {
          format!("{}发表了微博{}", self.username, self.content)
      }
  }
  
  // 特征约束 <T: Summary> 泛型T实际参数需要实现 Summary特征
  // 多个特征约束 <T: Summary + Display>
  // n个特征另种写法: where 关键字约束
  // fn some_function<T, U>(t: &T, u: &U) -> i32
  //     where T: Display + Clone + ...other traits,
  //     U: Clone + Debug
  // {}
  
  pub fn notify<T: Summary>(item: &T) {
      println!("Breaking news! {}", item.summarize());
  }
  
  // 可以通过 impl Trait 来说明一个函数返回了一个类型，该类型实现了某个特征
  // 特征类似golang的 interface, 不同的是golang仅支持动态分发, 而rust支持静态、动态分发两种
  // 参考： https://www.jianshu.com/p/43d998e6f79e
  fn returns_summarizable() -> impl Summary {
      Weibo {
          username: String::from("sunface"),
          content: String::from("m1 max太厉害了,电脑再也不会卡"),
      }
  }
  
  fn main() {
      let post = Post {
          title: "Rust语言简介".to_string(),
          author: "Sunface".to_string(),
          content: "Rust棒极了!".to_string(),
      };
      let weibo = Weibo {
          username: "sunface".to_string(),
          content: "好像微博没Tweet好用".to_string(),
      };
  
      println!("{}", post.summarize());
      println!("{}", weibo.summarize());
  }
  
  ```
  
  
  
- ### 特征对象

  ```rust
  // 可以通过 impl Trait 来说明一个函数返回了一个类型，该类型实现了某个特征
  // 特征类似golang的 interface, 不同的是golang仅支持动态分发, 而rust支持静态、动态分发两种
  // 参考： https://www.jianshu.com/p/43d998e6f79e
  fn returns_summarizable(switch: bool) -> Box<dyn Summary> {
      if switch {
          Box::new(Post {
              title: String::from("Penguins win the Stanley Cup Championship!"),
              author: String::from("Iceburgh"),
              content: String::from(
                  "The Pittsburgh Penguins once again are the best \
                   hockey team in the NHL.",
              ),
          })
      } else {
          Box::new(Weibo {
              username: String::from("horse_ebooks"),
              content: String::from("of course, as you probably already know, people"),
          })
      }
  }
  
  // wrong code
  // 函数返回值的生命周期必须与函数参数或函数体本身的生命周期相关联。
  // 函数 returns_summarizable1 试图返回一个指向局部变量的引用，这是不安全的，
  // 因为局部变量在函数返回后会被销毁，导致返回的引用成为悬垂指针（dangling pointer）
  // fn returns_summarizable1(switch: bool) -> &dyn Summary {
  //     if switch {
  //         &Post {
  //             title: String::from("Penguins win the Stanley Cup Championship!"),
  //             author: String::from("Iceburgh"),
  //             content: String::from(
  //                 "The Pittsburgh Penguins once again are the best \
  //                  hockey team in the NHL.",
  //             ),
  //         }
  //     } else {
  //         &Weibo {
  //             username: String::from("horse_ebooks"),
  //             content: String::from("of course, as you probably already know, people"),
  //         }
  //     }
  // }
  
  fn main() {
      // 特征对象指向实现了 xx 特征的类型的实例
      // &dyn Summary、Box<dyn Summary> 均为特征对象
      // Box<T> 在堆上分配对象
  
      // 当使用特征对象时，Rust 必须使用动态分发。编译器无法知晓所有可能用于特征对象代码的类型，所以它也不知道应该调用哪个类型的哪个方法实现
      // rust 细分了静态分发和动态分发, 所以一些代码可以通过静态分发在编译期间生成对应代码,运行时完全不影响性能
      // golang 的interface 则全部是动态分发, 即运行时才知道
  
      // 在 Rust 中，有两个self:
      // self 一个指代当前的实例对象
      // Self 一个指代特征或者方法类型的别名
  
      // 不是所有特征都能拥有特征对象，只有对象安全的特征才行。当一个特征的所有方法都有如下属性时，它的对象才是安全的：
      // 方法的返回类型不能是 Self
      // 方法没有任何泛型参数
  
      let s = returns_summarizable(true);
      s.summarize();
  
      let p = returns_summarizable(false);
      p.summarize();
  }
  
  ```
  
  
  
- ### 关联类型

  ```rust
  use std::fmt;
  
  // 关联类型是在特征定义的语句块中，申明一个自定义类型，这样就可以在特征的方法签名中使用该类型
  pub trait Iterator: AsRef<[u8]> + Clone + fmt::Debug + Eq {
      type Item: AsRef<[u8]> + Clone + fmt::Debug + Eq; // Item 是  Iterator特征的一个关联类型
  
      // Self 用来指代当前调用者的具体类型，那么 Self::Item 就用来指代该类型实现中定义的 Item 类型
      fn next(&mut self) -> Option<Self::Item>;
  }
  
  // 关联类型好处, 较泛型使用来说, 代码更简洁更方便阅读
  // demo:
  
  // 使用泛型
  trait Container<A, B> {
      fn contains(&self, a: A, b: B) -> bool;
  }
  
  // 使用泛型的话 相关函数头部 也要加入对应泛型声明<A，B> 可读性较差
  fn difference<A, B, C>(container: &C) -> i32
  where
      C: Container<A, B>,
  {
      123
  }
  
  // 使用关联类型
  trait Container1 {
      type A;
      type B;
      fn contains(&self, a: &Self::A, b: &Self::B) -> bool;
  }
  
  // 代码简洁的很
  fn difference1<C: Container1>(container: &C) {}
  
  ```
  
  
  
- ### 同名方法调用

  ```rust
  trait Pilot {
      fn fly(&self);
      fn baby_name() -> String;
  }
  
  trait Wizard {
      fn fly(&self);
      fn baby_name() -> String;
  }
  
  struct Human;
  
  impl Pilot for Human {
      fn fly(&self) {
          println!("This is your captain speaking.");
      }
      fn baby_name() -> String {
          "this is pilot baby".to_string()
      }
  }
  
  impl Wizard for Human {
      fn fly(&self) {
          println!("Up!");
      }
      fn baby_name() -> String {
          "wizard baby!".to_string()
      }
  }
  
  impl Human {
      fn fly(&self) {
          println!("*waving arms furiously*");
      }
  
      // Human 自身关联函数
      fn baby_name() -> String {
          "human baby!".to_string()
      }
  }
  
  fn main() {
      // Human结构体 , 实现了 Pilot 、 Wizard两个特征 , 两个特征都有 fly 方法
      // 甚至human结构体自身也实现了 fly 方法
      let person = Human;
      person.fly(); // 结构体自身方法调用  *waving arms furiously*
      Wizard::fly(&person); // 调用实现Wizard 特征的fly方法 Up!
      Pilot::fly(&person); // 调用实现Pilot 特征的fly方法 This is your captain speaking.
  
      // 关联函数同名调用
      // 在尖括号中，通过 as 关键字，我们向 Rust 编译器提供了类型注解，也就是 Pilot 就是 Human
      println!("pilot : {}", <Human as Pilot>::baby_name()); //pilot : this is pilot baby
      println!("wizard: {}", <Human as Wizard>::baby_name()); //wizard: wizard baby!
      println!("human: {}", Human::baby_name()) //human: human baby!
  }
  
  ```
  
  
  
- ### 特征定义中的特征约束

  ```rust
  // 有时，我们会需要让某个特征 A 能使用另一个特征 B 的功能(另一种形式的特征约束)，这种情况下，不仅仅要为类型实现特征 A，还要为类型实现特征 B 才行，这就是基特征( super trait )。
  use std::fmt::Display;
  
  // 要实现 OutlinePrint 特征前, 必须实现 Display特征
  // 写法类似特征约束: 只不过特征约束用在函数参数中 fn get<T:traitA>(x: T)
  trait OutlinePrint: Display {
      fn outline_print(&self) {
          let output = self.to_string();
          let len = output.len();
          println!("{}", "*".repeat(len + 4));
          println!("*{}*", " ".repeat(len + 2));
          println!("* {} *", output);
          println!("*{}*", " ".repeat(len + 2));
          println!("{}", "*".repeat(len + 4));
      }
  }
  fn get<T: OutlinePrint>(x: T) {}
  
  fn main() {}
  
  ```

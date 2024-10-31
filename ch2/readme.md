# 复合类型

- ### 字符串

  ```rust
  //  Rust 中的字符是 Unicode 类型，因此每个字符占据 4 个字节内存空间，但是在字符串中不一样，字符串是 UTF-8 编码，也就是字符串中的字符所占的字节数是变化的(1 - 4)
  // 字符串索引
  // Rust 不允许去索引字符串：因为索引操作，我们总是期望它的性能表现是 O(1)，然而对于 String 类型来说，无法保证这一点，因为 Rust 可能需要从 0 开始去遍历字符串来定位合法的字符
  
  fn main() {
      // String 与 &str 的转换
      // &str -> String
      _ = String::from("hello,world");
      "hello,world".to_string();
  
      // String -> &str
      let s = String::from("hello,world!");
      say_hello(&s);
      say_hello(&s[..]);
      say_hello(s.as_str());
  
      fn say_hello(s: &str) {
          println!("{}", s);
      }
  
      // 追加
      let mut s = String::from("Hello ");
  
      s.push_str("rust");
      println!("追加字符串 push_str() -> {}", s); // 追加字符串 push_str() -> Hello rust
      s.push('!');
      println!("追加字符 push() -> {}", s); //追加字符 push() -> Hello rust!
  
      // 插入 追加插入都是基于原字符串修改 , 所以必须是mut 变量
      let mut s = String::from("Hello rust!");
      s.insert(5, ',');
      println!("插入字符 insert() -> {}", s); // 插入字符 insert() -> Hello, rust!
      s.insert_str(6, " I like");
      println!("插入字符串 insert_str() -> {}", s); //插入字符串 insert_str() -> Hello, I like rust!
  
      // 替换
      // 替换返回一个新的字符串, 所以无需mut
      let new_string_replace: String;
      let string_replace = String::from("I like rust. Learning rust is my favorite!");
      new_string_replace = string_replace.replace("rust", "RUST");
      println!("replace res is {new_string_replace}"); // replace res is I like RUST. Learning RUST is my favorite!
      let new_string_replacen = string_replace.replacen("rust", "RUST", 1); // 第三个参数表示替换个数
      println!("replacen res is {new_string_replacen}"); // replacen res is I like RUST. Learning rust is my favorite!
  
      // replace_range 该方法是直接操作原来的字符串，不会返回新的字符串
      let mut string_replace_range = String::from("I like rust!");
      string_replace_range.replace_range(7..8, "R");
      println!("replace_range res is: {string_replace_range}"); // replace_range res is: I like Rust
  
      // 删除 pop remove truncate clear方法
      // pop —— 删除并返回字符串的最后一个字符
      // 其返回值是一个 Option 类型，如果字符串为空，则返回 None
      let mut string_pop = String::from("rust pop 中文!");
      let p1 = string_pop.pop();
      let p2 = string_pop.pop();
      dbg!(p1); //  p1 = Some('!',)
      dbg!(p2); //  p2 = Some('文',)
      dbg!(string_pop); // string_pop = "rust pop 中"
  
      // remove —— 删除并返回字符串中指定位置的字符
      // remove() 方法是按照字节来处理字符串的，如果参数所给的位置不是合法的字符边界，则会发生错误
      let mut string_remove = String::from("测试remove方法");
      println!(
          "string_remove 占 {} 个字节",
          std::mem::size_of_val(string_remove.as_str()) // string_remove 占 18 个字节
      );
      // 删除第一个汉字
      string_remove.remove(0);
      // 下面代码会发生错误
      // string_remove.remove(1);
      // 直接删除第二个汉字
      // string_remove.remove(3);
      dbg!(string_remove); // string_remove = "试remove方法"
  
      // truncate —— 删除字符串中从指定位置开始到结尾的全部字符
      // 该方法是直接操作原来的字符串。无返回值
      let mut string_truncate = String::from("测试truncate");
      string_truncate.truncate(3);
      dbg!(string_truncate); // string_truncate = "测"
  
      // clear —— 清空字符串
      let mut string_clear = String::from("string clear");
      string_clear.clear();
      dbg!(string_clear); // string_clear = ""
  
      // 字符串链接 + += 或者 format!
      let string_append = String::from("hello ");
      let string_rust = String::from("rust");
      // &string_rust会自动解引用为&str
      let result = string_append + &string_rust;
      let mut result = result + "!"; // `result + "!"` 中的 `result` 是不可变的
      result += "!!!";
      println!("连接字符串 + -> {}", result); // 连接字符串 + -> hello rust!!!!
  
      let s1 = "hello";
      let s2 = String::from("rust");
      let s = format!("{} {}!", s1, s2);
      println!("{}", s); // hello rust!
  
      for c in "中国人".chars() {
          println!("{}", c); // 中国人
      }
  
      //
      for b in "中国人".bytes() {
          println!("{}", b); // 228 184 173 229 155 189 228 186 186
      }
  }
  ```

  

- ### 切片

  ```rust
  let s = String::from("hello");
  
  let slice = &s[0..2];
  let slice = &s[..2];
  
  // 在对字符串使用切片语法时需要格外小心，切片的索引必须落在字符之间的边界位置，也就是 UTF-8 字符的边界，例如中文在 UTF-8 中占用三个字节，下面的代码就会崩溃：
   let s = "中国人";
   let a = &s[0..2];
   println!("{}",a);
  ```
  
  
  
- ### 元组

  ```rust
  fn main() {
      let tup: (i32, f64, u8) = (500, 6.4, 1);
      let (x, y, z) = tup;
  
      println!("The value of y is: {}", y);
      let _five_hundred = tup.0;
      let _six_point_four = tup.1;
      let _one = tup.2;
  }
  ```
  
  
  
- ### 结构体

  ```rust
  struct User {
      active: bool,
      username: String,
      email: String,
      sign_in_count: u64,
  }
  
  fn build_user(email: String, username: String) -> User {
      User {
          email,
          username,
          active: true,
          sign_in_count: 1,
      }
  }
  fn main() {
      // 初始化实例时，每个字段都需要进行初始化
      let mut user1 = User {
          email: String::from("someone@example.com"),
          username: String::from("someusername123"),
          active: true,
          sign_in_count: 1,
      };
      user1.email = String::from("anotheremail@example.com");
  
      let user2 = User {
          email: String::from("another@example.com"),
          ..user1 // .. 语法表明凡是我们没有显式声明的字段，全部从 user1 中自动获取
      };
  
      // 元组结构体(Tuple Struct)
      struct Color(i32, i32, i32);
      struct Point(i32, i32, i32);
  
      let _black = Color(0, 0, 0);
      let _origin = Point(0, 0, 0);
  }
  
  ```

  

- ### 枚举类型

  ```rust
  enum Message {
      Quit,
      Move { x: i32, y: i32 },
      Write(String),
      ChangeColor(i32, i32, i32),
  }
  
  fn main() {
      let m1 = Message::Quit;
      let m2 = Message::Move { x: 1, y: 1 };
      let m3 = Message::ChangeColor(255, 255, 0);
  
      // Option 枚举包含两个成员，一个成员表示含有值：Some(T), 另一个表示没有值：None，定义如下：
      enum Option<T> {
          Some(T),
          None,
      }
  }
  
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
  
  ```

  

- ### 数组

  ```rust
  fn main() {
      // 数组定义
      let a = [1, 2, 3, 4, 5];
      let a: [i32; 5] = [1, 2, 3, 4, 5];
      let a = [3; 5]; // means [3, 3, 3, 3, 3]
  
      // 数组类型容易跟数组切片混淆，[T;n] 描述了一个数组的类型，而 [T] 描述了切片的类型， 因为切片是运行期的数据结构，它的长度无法在编译期得知，因此不能用 [T;n] 的形式去描述
      // [u8; 3]和[u8; 4]是不同的类型，数组的长度也是类型的一部分
      // 在实际开发中，使用最多的是数组切片[T]，我们往往通过引用的方式去使用&[T]，因为后者有固定的类型大小
  }
  
  ```

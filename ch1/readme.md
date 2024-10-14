# 基本类型

- ### 整数类型

  | 长度       | 有符号类型 | 无符号类型 |
  | ---------- | ---------- | ---------- |
  | 8 位       | `i8`       | `u8`       |
  | 16 位      | `i16`      | `u16`      |
  | 32 位      | `i32`      | `u32`      |
  | 64 位      | `i64`      | `u64`      |
  | 128 位     | `i128`     | `u128`     |
  | 视架构而定 | `isize`    | `usize`    |

  

  ```rust
  //  Rust 整型默认使用 i32
  fn main() {
      let _i = 1; // default i32
      let _j: i64 = 1_000_000; // 1000000
  
      // 整数溢出
      let j: i8 = 127;
      println!("{:?}", j.wrapping_add(3)); // -126 wrapping_* 方法在所有模式下都按照补码循环溢出规则处理
      println!("{:?}", j.overflowing_add(3)); // -126, true overflowing_* 方法返回该值和一个指示是否存在溢出的布尔值
      println!("{:?}", j.checked_add(3)); // None 使用 checked_* 方法时发生溢出，则返回 None 值
      println!("{:?}", j.saturating_add(3)) // 127 saturating_* 方法，可以限定计算后的结果不超过目标类型的最大值或低于最小值
  }
  ```

  

- ### 浮点数类型

  ```rust
  fn main() {
      // 浮点类型
      // Rust 中浮点类型数字也有两种基本类型： f32 和 f64，分别为 32 位和 64 位大小。默认浮点类型是 f64
      let _x: f32 = 1.0;
      let _y = 1.0; // default f64
  
      // 浮点数陷阱
      // 浮点数往往是你想要数字的近似表达 浮点数类型是基于二进制实现的
      // 注: 避免在浮点数上测试相等性
      // 注: 当结果在数学上可能存在未定义时，需要格外的小心
      assert!(0.1_f32 + 0.2_f32 == 0.3_f32); // true f32精度没那么高
      assert!(0.1_f64 + 0.2_f64 == 0.3_f64); // panic f64因为二进制精度问题，导致了 0.1 + 0.2 并不严格等于 0.3，它们可能在小数点 N 位后存在误差。
  
      // 未定义的数值
      // Rust 的浮点数类型使用 NaN
      let x = (-42.0_f32).sqrt();
      if x.is_nan() {
          println!("未定义的数学行为")
      }
  }
  
  ```

  

- ### 数值运算

  | 运算符  | 说明                                                   |
  | ------- | ------------------------------------------------------ |
  | & 位与  | 相同位置均为1时则为1，否则为0                          |
  | \| 位或 | 相同位置只要有1时则为1，否则为0                        |
  | ^ 异或  | 相同位置不相同则为1，相同则为0                         |
  | ! 位非  | 把位中的0和1相互取反，即0置为1，1置为0                 |
  | << 左移 | 所有位向左移动指定位数，右位补0                        |
  | >> 右移 | 所有位向右移动指定位数，带符号移动（正数补0，负数补1） |

  ```rust
  fn main() {
      // 加法
      let _sum = 5 + 10;
      // 减法
      let _difference = 95.5 - 4.3;
      // 乘法
      let _product = 4 * 30;
      // 除法
      let _quotient = 56.7 / 32.2;
      // 求余
      let _remainder = 43 % 5;
  
      // 位运算
      // 二进制为00000010
      let a: i32 = 2;
      // 二进制为00000011
      let b: i32 = 3;
      println!("(a & b) value is {}", a & b); // 2
      println!("(a | b) value is {}", a | b); // 3
      println!("(a ^ b) value is {}", a ^ b); // 1
      println!("(!b) value is {} ", !b); // -4
      println!("(a << b) value is {}", a << b); // 16
      println!("(a >> b) value is {}", a >> b); // 0
  
      let mut a = a;
      // 注意这些计算符除了!之外都可以加上=进行赋值 (因为!=要用来判断不等于)
      a <<= b;
      println!("(a << b) value is {}", a); // 16
  
      // 序列遍历
      for i in 2..5 {
          println!("{}", i); // 依次打印 2 3 4
      }
      for i in 2..=5 {
          println!("{}", i); // 依次打印 2 3 4 5
      }
  }
  
  ```

  

- ### 字符类型

  ```rust
  fn main() {
      // 字符类型
      // Rust 的字符不仅仅是 ASCII，所有的 Unicode 值都可以作为 Rust 字符，包括单个的中文、日文、韩文、emoji 表情符号等
      // Unicode 都是 4 个字节编码，因此字符类型也是占用 4 个字节
      let x = '中';
      println!("字符'中'占用了{}字节的内存大小", std::mem::size_of_val(&x));
  }
  
  ```

  

- ### 布尔类型

  ```rust
  fn main() {
      // bool类型
      // 布尔值占用内存的大小为 1 个字节：
      let _t = true;
      let f: bool = false;
      if f {
          println!("这是段毫无意义的代码");
      }
  }
  
  ```

  

- ### 单元类型

  ```rust
  fn main() {
      // 单元类型
      // 单元类型就是 ()
      // 和 Go 语言的 struct{} 类似，可以作为一个值用来占位，但是完全不占用任何内存。
  }
  
  ```

  

- ### 语句

  ```rust
  fn main() {
      // 以下都是语句，它们完成了一个具体的操作，但是并没有返回值，因此是语句
      let _a = 8;
      let _b: Vec<f64> = Vec::new();
      let (_a, _c) = ("hi", false);
  }
  
  ```

  

- ### 表达式

  ```rust
  fn main() {
      let y = {
          let x = 3; // 语句
          x + 1 // 表达式
      };
  
      println!("The value of y is: {}", y);
  }
  
  ```

  

- ### 函数

  ```rust
  fn main() {
      // 函数名和变量名使用蛇形命名法(snake case)，例如 fn add_two() -> {}
      // 函数的位置可以随便放，Rust 不关心我们在哪里定义了函数，只要有定义即可
      // Rust 是强类型语言，因此需要你为每一个函数参数都标识出它的具体类型
      println!("add res is : {}", add_demo(1, 7));
  }
  
  fn add_demo(i: i32, j: i32) -> i32 {
      i + j
  }
  
  ```

  
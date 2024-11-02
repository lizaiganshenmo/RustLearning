# 集合类型

- ### 动态数组Vector

  ```rust
  #[derive(Debug)]
  struct Person {
      name: String,
      age: u32,
  }
  
  impl Person {
      fn new(name: String, age: u32) -> Person {
          Person { name, age }
      }
  }
  
  fn main() {
      // 创建数组
      let v: Vec<i32> = Vec::new();
      // 使用宏 vec! 来创建数组
      let mut v = vec![1, 2, 3];
      v.push(4);
  
      // 索引访问
      let third = v[2];
      println!("第三个元素是 {}", third);
  
      // get访问
      match v.get(5) {
          Some(third) => println!("第三个元素是 {third}"),
          None => println!("去你的第三个元素，根本没有！"),
      }
  
      //
      let first = &v[0];
      // 数组的大小是可变的，当旧数组的大小不够用时，Rust 会重新分配一块更大的内存空间，然后把旧数组拷贝过来。
      // 这种情况下，之前的引用显然会指向一块无效的内存，这非常 rusty
      // v.push(6); //  cannot borrow `v` as mutable because it is also borrowed as immutable
      println!("The first element is: {first}");
  
      let mut v = vec![1, 2, 3];
      for i in &mut v {
          *i += 10
      }
  
      let mut v = Vec::with_capacity(10);
      v.extend([1, 2, 3]); // 附加数据到 v
      println!("Vector 长度是: {}, 容量是: {}", v.len(), v.capacity()); // Vector 长度是: 3, 容量是: 10
  
      v.reserve(100); // 调整 v 的容量，至少要有 100 的容量
      println!(
          "Vector(reserve)长度是: {}, 容量是: {}",
          v.len(),
          v.capacity()
      ); // Vector(reserve)长度是: 3, 容量是: 103
  
      v.shrink_to_fit(); // 释放剩余的容量，一般情况下，不会主动去释放容量
      println!(
          "Vector(shrink_to_fit) 长度是: {}, 容量是: {}",
          v.len(),
          v.capacity()
      ); // Vector(shrink_to_fit) 长度是: 3, 容量是: 3
  
      // vector 常见方法
      let mut v = vec![1, 2];
      assert!(!v.is_empty()); // 检查 v 是否为空
  
      v.insert(2, 3); // 在指定索引插入数据，索引值不能大于 v 的长度， v: [1, 2, 3]
      assert_eq!(v.remove(1), 2); // 移除指定位置的元素并返回, v: [1, 3]
      assert_eq!(v.pop(), Some(3)); // 删除并返回 v 尾部的元素，v: [1]
      assert_eq!(v.pop(), Some(1)); // v: []
      assert_eq!(v.pop(), None); // 记得 pop 方法返回的是 Option 枚举值
      v.clear(); // 清空 v, v: []
  
      let mut v1 = [11, 22].to_vec(); // append 操作会导致 v1 清空数据，增加可变声明
      v.append(&mut v1); // 将 v1 中的所有元素附加到 v 中, v1: []
      v.truncate(1); // 截断到指定长度，多余的元素被删除, v: [11]
      v.retain(|x| *x > 10); // 保留满足条件的元素，即删除不满足条件的元素
  
      let mut v = vec![11, 22, 33, 44, 55];
      // 删除指定范围的元素，同时获取被删除元素的迭代器, v: [11, 55], m: [22, 33, 44]
      let mut m: Vec<_> = v.drain(1..=3).collect();
  
      let v2 = m.split_off(1); // 指定索引处切分成两个 vec, m: [22], v2: [33, 44]
  
      let v = vec![11, 22, 33, 44, 55];
      let slice = &v[1..=3];
      assert_eq!(slice, &[22, 33, 44]);
  
      // vector排序
      // 非稳定 排序的算法的速度会优于 稳定 排序算法，同时，稳定 排序还会额外分配原数组一半的空间。
      let mut vec = vec![1, 5, 10, 2, 15];
      vec.sort_unstable();
      assert_eq!(vec, vec![1, 2, 5, 10, 15]);
  
      let mut people = vec![
          Person::new("Zoe".to_string(), 25),
          Person::new("Al".to_string(), 60),
          Person::new("John".to_string(), 1),
      ];
      // 定义一个按照年龄倒序排序的对比函数
      people.sort_unstable_by(|a, b| b.age.cmp(&a.age));
  
      println!("{:?}", people); // [Person { name: "Al", age: 60 }, Person { name: "Zoe", age: 25 }, Person { name: "John", age: 1 }]
  }
  
  
  ```
  
  
  
- ### HashMap

  ```rust
  use std::collections::HashMap;
  fn main() {
      // 创建map
      let mut my_gems = HashMap::new();
      // 将宝石类型和对应的数量写入表中
      my_gems.insert("红宝石", 1);
      my_gems.insert("蓝宝石", 2);
  
      let mut m = HashMap::with_capacity(100);
      m.insert("k", 3);
  
      let teams_list = vec![
          ("中国队".to_string(), 100),
          ("美国队".to_string(), 10),
          ("日本队".to_string(), 50),
      ];
  
      // 先将 Vec 转为迭代器，接着通过 collect 方法，将迭代器中的元素收集后，转成 HashMap
      // let teams_list: Vec<_> = teams_list.into_iter().collect();
      let teams_map: HashMap<_, _> = teams_list.into_iter().collect();
  
      println!("{:?}", teams_map); // {"美国队": 10, "日本队": 50, "中国队": 100}
  
      // 查询
      let mut scores = HashMap::new();
      scores.insert(String::from("Blue"), 10);
      scores.insert(String::from("Yellow"), 50);
  
      let team_name = String::from("Blue");
      let Some(score) = scores.get(&team_name) else {
          return;
      };
      println!("score is:{}", score);
  
      // 更新
      let mut scores = HashMap::new();
      scores.insert("Blue", 10);
      let old = scores.insert("Blue", 20);
      assert_eq!(old, Some(10));
      // 查询新插入的值
      let new = scores.get("Blue");
      assert_eq!(new, Some(&20));
      // 查询Yellow对应的值，若不存在则插入新值
      let v = scores.entry("Yellow").or_insert(5);
      assert_eq!(*v, 5); // 不存在，插入5
  
      // 查询Yellow对应的值，若不存在则插入新值
      let v = scores.entry("Yellow").or_insert(50);
      assert_eq!(*v, 5); // 已经存在，因此50没有插入
  }
  
  ```
  

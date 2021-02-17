## map 的底层实现是哈希表

map 是一种拥有 Key-Value (键值对)的元素的无序集合。

注意，它是无序的，主要原因就是它的底层实现是哈希表或者叫散列表。哈希表是根据 Key 值映射 Value 值的一种数据结构，Value 在内存中存储完全根据 Key 值随机而定，无法保证它是有序的。

map 中所有的键都要求具有相同的数据类型，所有的值也是，但键和值的类型不一定相同。

并且键的类型必须是不可变的，是可以通过操作符 `==` 来进行比较的，如切片、函数，以及包含切片的结构类型是可变的类型，就不能作为键。而值类型则没有任何限制。

> 浮点型虽然也是可比较的，但浮点型有精度的问题，不推荐使用。



## map 的创建和初始化

声明一个 map，这个映射是是一个 nil map：

```go
var m map[int]string
m[0] = "hello" // error, assignment to entry in nil map
```

往 nil map 增加键值对会生成运行时错误。



正确的做法应该声明一个空切片：

```go
m := map[int]string{}
// 或者
m := make(map[int]string)
m[0] = "hello"
m[1] = "world"
fmt.Println(m) // map[0:hello 1:world]
```

一般推荐使用 make 来创建 map。



也可以使用字面量的方式来创建一个带初始化键值对元素的 map，如：

```go
m := map[int]string {
  4: "hello",
  2: "world",
}
```

> 注意，每行后面需要使用 `,` 符号来分隔。

## map 的使用

**访问 map 的元素：**

使用 Key 为下标的方式访问：

```go
ages := map[string]int {
  "alice": 20,
  "bob": 32,
}
value := ages["bob"] // 32
```

这种方式实际上是返回两个值，一个是 Key 对应的 Value，一个是表示这个 Key 是否存在 Value 的标志。

可以用此方式来查找是否存在 Key：

```go
value, exists := ages["bob"]
if exists {
  fmt.Println(value) // 32
}
```

> 注意，如果 Key 不存在，也会返回该 Value 对应类型的零值，不会报错。



**增加 map 的元素：**

```go
ages["joe"] = 54
ages["bob"] = 25 // 相同 Key 会覆盖原来的 Value
```



**删除 map 的元素：**

使用 Go 语言内置的 delete 函数，可以删除一个 Key 值，自然对应 Value 也被删除：

```go
delete(ages, "bob")
fmt.Println(ages) // map[alice:20 joe:54]
```



**迭代 map 的元素：**

同样使用 `for...range` 的方式：

```go
// 同时返回 key-value
for k, v := range m {
    fmt.Println(k, v) // 打印是无序的
}

// 省略 value，只返回 key
for k := range m {
    fmt.Println(k)
}

// 省略 key，只返回 value
for _, v := range m {
    fmt.Println(v)
}
```



## 在函数间传递 map

在函数间传递 map，传递的是 map 的引用，即引用传递，和切片是一样的。

这意味着如果在函数中修改了 map 的值，原值也会相应被修改。

```go
func main() {
  ages := map[string]int {
    "alice": 20,
    "bob": 32,
    "joe": 54,
	}
  
  removeValue(ages, "joe")
  
  for k, v := range ages {
    fmt.Printf("key: %s, value: %d\n", k, v)
    // 输出
    // key: alice, value: 20
		// key: bob, value: 32
  }
}

func removeValue(ages map[string]int, key string) {
  delete(ages, key)
}
```

## map 实现集合 set

像 C++ 等语言，有 Set 集合的概念，即只含键(Key)值的 map。

在 Go 语言中没有 Set 的概念，但可以通过 map 来模拟，如下：

```go
s := make(map[int]bool)
```

因为 bool 类型一般没有实际的意义，所以把一个 map 的 Value 值设为 bool 类型，即可表示一个集合。



## 复杂的 map

以上 map 的值类型都是简单数据类型，但 map 的值类型是没有限制的，也可以是复杂数据类型，例如 slice 或 map，如下声明一个值类型又是 map 类型的 map：

```go
graph := make(map[string]map[string]bool)

value := graph[from][to] // get
```

这个 map 可以用来表示一个图，`graph[from][to]` 就表示某一条边是否存在。



## 总结

map 的底层实现是哈希表

空 map 和 nil map

map 的增删查改

map 在函数间传递的是引用

map 的值类型作为复杂数据类型，可以表示更多复杂的数据结构。
### 记录一下go的学习

#### 创建变量

变量有四种创建方式，其中第一种和第二种可以在创建的时候定义类型，而第三种直接用var，会自动识别类型，第四种是最简洁的一种，推荐使用。注意：main方法所在的文件应该是package main

```go
var a int
a = 100
fmt.Println("a = ", a)

var b int = 200
fmt.Println("b = ", b)

var c = 300
fmt.Println("c = ", c)

d := 400
fmt.Println("d = ", d)
```

创建多个变量可以直接使用逗号分隔，对于不同类型可以使用var()，如下

```go
import "fmt"

func main() {
	var a, b= 1, 2
	fmt.Printf("a = %d, b = %d\n", a, b) // a = 1, b = 2
	var(
		c, d int = 3, 4
		e, f bool = true, false
	)
	fmt.Printf("c = %d, d = %d\n", c, d) // c = 3, d = 4
	fmt.Printf("e = %t, f = %t\n", e, f) // e = true, f = false
}
```

#### 创建常量

常量可以在方法中定义，也可以使用const()定义多个常量，其中iota代表从0开始递增，每次使用的时候加一，以行为最小单位。

```go
package main

import "fmt"

const (
	A, B = iota + 1, iota + 2
	C, D
	E, F = iota * 1, iota * 2
	G, H
)

func main()  {
	const a int = 1
	const b = 2
	fmt.Printf("a = %d, b = %d\n", a, b) // a = 1, b = 2
	fmt.Printf("A = %d, B = %d\n", A, B) // A = 1, B = 2
	fmt.Printf("C = %d, D = %d\n", C, D) // C = 2, D = 3
	fmt.Printf("E = %d, F = %d\n", E, F) // E = 2, F = 4
	fmt.Printf("G = %d, H = %d\n", G, H) // G = 3, H = 6
}
```

### 函数

go语言中函数返回可以返回多个值，使用()定义返回类型，值得注意的是：go语言并没有函数重载

```go
import "fmt"

func doubleNum(a int) int{
	return 2 * a
}

/**
返回多个参数，使用()定义返回类型
 */
func doubleNum2(a int, b int) (int, int) {
	return a * 2, b * 2
}

/**
返回多个参数，使用()定义类型和变量名，
返回时只需要return，会自动将r1、r2返回，不需要加上返回值
 */
func doubleNum3(a int, b int) (r1 int, r2 int) {
	r1 = 2 * a
	r2 = 2 * b
	return
}

func main() {
	fmt.Printf("result:%d\n", doubleNum(10)) // result:20
	a, b := doubleNum2(20, 30)
	c, d := doubleNum3(40, 50)
	fmt.Printf("a = %d, b = %d\n", a, b) // a = 40, b = 60
	fmt.Printf("c = %d, d = %d\n", c, d) // c = 80, d = 100
}
```

init()函数
go语言中执行函数时会先去检查导的包，导入后会执行包的init()函数，最后在main方法之前会执行该go文件的init()函数

### 导包

go语言中导包的使用import()，并且允许使用别名，其中有两个特殊的别名，分别是"_"和"."，前者代表匿名导入，也就是不使用该包，但是会执行init()方法，后者代表将导入的包的方法复制到该文件下，这样就可以直接使用方法名。注意：方法名首字母大写才能被调用，小写是私有的。

```go
import (
	l "goStudy/study/lib1"
	_ "goStudy/study/lib2"
	. "goStudy/study/lib3"
)

func main()  {
	l.Lib1Test()
	Lib3Test()
}

输出：
Lib1 init() ...
Lib2 init() ...
Lib3 init() ...
Lib1 Test
Lib3 Test
```

### 指针

指针这里就不多说了，至于是啥，学过C/C++的都知道，一个意思

```
import "fmt"

func main() {
	a := 10
	change(&a)
	fmt.Println(a) // 20
}

func change(a *int) {
	*a *= 2
}
```

### defer关键字

defer关键字调用方法，可以在当前函数执行完毕时调用，如果有多个defer，会将这些方法进栈，所有调用顺序是先定义的后执行，defer是整个方法结束后执行，所以它会在return之后在去执行

```go
import "fmt"

func deferTest() int{
	defer deferFunc()
	return returnFunc()
}

func deferFunc()  {
	fmt.Println("deferFunc ...")
}

func returnFunc() int {
	fmt.Println("returnFunc ...")
	return 0
}

func main() {
	defer fmt.Println("A")
	defer fmt.Println("B")
	deferTest()
}

输出：
returnFunc ...
deferFunc ...
B
A
```

### slice切片

go中定义数组时，如果给了长度就是长度固定的数组，如果没有就是slice切片，固定长度的数组在使用时非常不方便，特别是在方法调用时，形参是多少长度的，那就只能调用多少长度的，而且只是值传递，而slice是动态长度，不存在第一个问题，并且它是引用传递。

```go
import "fmt"

func doubleArray(arr []int)  {
	for i:=0; i < len(arr); i++ {
		arr[i] *= 2
	}
}

func main() {
	a := []int{1, 2, 3}
	doubleArray(a)
	for index, value := range a {
		fmt.Printf("a[%d] = %d\n", index, value)
	}
}

输出：
a[0] = 2
a[1] = 4
a[2] = 6
```

make()可以为slice切片开辟一个内存空间，其默认值为0，判断其是否是空使用nil，而不是null，if ... else的使用要注意else后面的左括号必须与else同一行，否则会报错。

```go
func main() {
	slice1 := make([]int, 3)
	var slice2 []int
	printSlice(slice1) // slice = [0 0 0], length = 3
	printSlice(slice2) // slice是一个空切片
}

func printSlice(sliceArg []int)  {
	if sliceArg == nil {
		fmt.Println("slice是一个空切片")
	} else {
		fmt.Printf("slice = %v, length = %d\n", sliceArg, len(sliceArg))
	}
}
```

在使用make开辟空间的时候，其实还有一个参数，是切片的容量，注意：容量并非长度。实际上有点类似于指针，slice有个头指向第一个数据，尾指向长度所在数据，每次追加数据时，尾指针后移一位，如果此时超出容量，则会扩容，容量为之前容量的2倍，如果make时不定义容量大小，那么默认为长度的大小

```go
import "fmt"

func main() {
	s := make([]int, 3, 4) // 创建长度为3，容量为4的切片
	fmt.Printf("s = %v, len = %d, cap = %d\n", s, len(s), cap(s)) // s = [0 0 0], len = 3, cap = 4
	s = append(s, 1) // 追加数据
	fmt.Printf("s = %v, len = %d, cap = %d\n", s, len(s), cap(s)) // s = [0 0 0 1], len = 4, cap = 4
	s = append(s, 2) // 此时超出容量，扩容
	fmt.Printf("s = %v, len = %d, cap = %d\n", s, len(s), cap(s)) // s = [0 0 0 1 2], len = 5, cap = 8
}
```

使用冒号可以截取slice，注意：截取之后的切片数据地址本质上还是之前的，只是多了指针指向不同位置，如果想开辟一个新的空间，可以先make一个切片，再使用copy进行深拷贝。

```go
import "fmt"

func main() {
	s := make([]int, 3)
	s1 := s[0:2]
	s1[0] = 1
	fmt.Printf("s = %v\n", s) // s = [1 0 0]

	s2 := make([]int, 3)
	copy(s2, s)
	s2[0] = 2
	fmt.Printf("s = %v", s) // s = [1 0 0]
}
```

### map

map跟java中意思差不多。在go中定义map同样需要make()开辟空间，或者使用map{}来直接初始化。

```go
func main() {
	var map1 map[string]string
	map1 = make(map[string]string, 5)
	map1["a"] = "A"
	map1["b"] = "B"
	map1["c"] = "C"
	fmt.Println(map1)

	map2 := make(map[string]string)
	map2["a"] = "A"
	map2["b"] = "B"
	map2["c"] = "C"
	fmt.Println(map2)

	map3 := map[string]string{
		"a": "A",
		"b": "B",
		"c": "C",
	}
	fmt.Println(map3)
}
```

map的基本操作，注意：map在方法形参中属于引用传递

```go
func main() {
	map1 := map[string]string {
		"药水哥" : "鼷鼠霸王",
		"蔡徐坤" : "鸡你太美",
		"马老师" : "闪电五连鞭",
	}

	// 增
	map1["大司马"] = "芜湖南桐"
	// 删
	delete(map1, "蔡徐坤")
	// 改
	map1["药水哥"] = "行为艺术家"
	// 查，同上
	// 遍历
	for key, value := range map1 {
		fmt.Println("key = ", key)
		fmt.Println("value = ", value)
	}
}

输出：
key =  大司马
value =  芜湖南桐
key =  药水哥
value =  行为艺术家
key =  马老师
value =  闪电五连鞭
```

### struct 结构体

有点类似于C/C++中的结构体。注意：struct方法传递时只是参数，想要引用需使用指针。

```go
import "fmt"

type Book struct {
	title string
	author string
}

func main() {
	book1 := Book{
		title:  "逆天邪神",
		author: "火星引力",
	}
	book2 := Book{
		title: "斗破苍穹",
		author: "天蚕土豆",
	}
	fmt.Println(book1) // {逆天邪神 火星引力}
	fmt.Println(book2) // {斗破苍穹 天蚕土豆}
	change1(book1)
	change2(&book2)
	fmt.Println(book1) // {逆天邪神 火星引力}
	fmt.Println(book2) // {伏天氏 净无痕}
}

func change1(book Book)  {
	book.title = "武动乾坤"
	book.author = "天蚕土豆"
}

func change2(book *Book)  {
	book.title = "伏天氏"
	book.author = "净无痕"
}
```

### 面向对象设计

#### 封装

Go语言中大写开头表示公共，小写开头表示私有，在面向对象设计中，很多属性是不可以被直接获取的，需要暴露一些提供操作的方法。

```go
import "fmt"

type Person struct {
	name string
	age int
}

func (this *Person) ToString()  {
	fmt.Println("[ name =", this.name, ", age =", this.age, "]")
}

func (this *Person) getName() string {
	return this.name
}

func (this *Person) setName(name string) {
	this.name = name
}

func main() {
	lbw := Person{
		name: "卢本伟",
		age: 18,
	}
	lbw.ToString() // [ name = 卢本伟 , age = 18 ]
	lbw.setName("五五开")
	lbw.ToString() // [ name = 五五开 , age = 18 ]
}
```

#### 继承

go语言中继承只需要在struct域中加上需要继承的struct即可，注意只是加上类型，没有变量名

```go
import "fmt"

type Pet struct {
	name string
}

func (this *Pet) eat() {
	fmt.Println("I cat eat")
}

type Dog struct {
	Pet
	legs int
}

func (this *Dog) bark() {
	fmt.Println("I can bark")
}

func (this *Dog) ToString() {
	fmt.Println("name =", this.name)
}

func main() {
	dog := Dog{Pet{"泰日天"}, 4}
	dog.ToString() // name = 泰日天
	dog.bark() // I can bark
	dog.eat() // I cat eat

	var dog2 Dog
	dog2.name = "狗子"
	dog2.legs = 4
	dog2.ToString() // name = 狗子
}
```

#### 多态

Go语言中的多态在创建对象时，使用&地址。和其他面向对象语言一样，需要实现所有父类（interface）的方法。

```go
import "fmt"

type Human interface {
	Show()
}

type Programmer struct {
	hairNumber int
}

func (this *Programmer) Show() {
	fmt.Println("我是一名程序员，我有", this.hairNumber, "根头发")
}

type Capitalist struct {
	money int
}

func (this *Capitalist) Show()  {
	fmt.Println("我是一名资本家，我有", this.money, "亿资产")
}

func main() {
	var human1 Human
	var human2 Human
	human1 = Human(&Programmer{hairNumber: 10000000})
	human2 = &Capitalist{money: 5}
	human1.Show() // 我是一名程序员，我有 10000000 根头发
	human2.Show() // 我是一名资本家，我有 5 亿资产
}
```

#### 空接口（万能接口）和断言

空接口用interface{}表示，有点类似于java的Object类，属于所有类的父类，断言：.()，类似于java的instanceof，返回两个值，第二个值为是否属于该类型，第一个值为该变量，第一个值是否有数据的前提是第二个值为true

```go
import "fmt"

func doFunc(arg interface{}) {
	value, ok := arg.(string)
	if ok {
		fmt.Println("arg是string类型:", value)
	} else {
		fmt.Println("arg不是string类型")
	}
}

func main() {
	doFunc("123") // arg是string类型: 123
	doFunc(123) // arg不是string类型
}
```

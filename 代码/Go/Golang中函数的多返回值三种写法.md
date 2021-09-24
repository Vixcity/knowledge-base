```go 
package main

import "fmt"

func foo1(a string,b int) int {
	 fmt.Println("a = ",a)
	 fmt.Println("b = ",b)

	 c := 100
	 return c
}
  
// 返回多个匿名返回值
func foo2(a string,b int) (int, int) {
	 fmt.Println("a = ",a)
	 fmt.Println("b = ",b)

	 return 666,777
}

// 返回多个具名返回值
func foo3(a string,b int) (r1 int,r2 int) {
	 fmt.Println("a = ",a)
	 fmt.Println("b = ",b)

	 // r1,r2属于形参，默认初始化是0
	 // r1,r2作用空间是foo3，函数体的内部空间
	 fmt.Println("r1 = ",r1) //0
	 fmt.Println("r2 = ",r2) //0  

	 // 给有名称的返回值变量赋值
	 r1 = 1000
	 r2 = 2000

	 return
}

// 返回多个具名返回值
func foo4(a string,b int) (r1,r2 int) {
	 fmt.Println("a = ",a)
	 fmt.Println("b = ",b)

	 // 给有名称的返回值变量赋值
	 r1 = 1000
	 r2 = 2000

	 return
}

func main ()  {
	 c := foo1("abc",555)
	 d,f := foo2("asd",666)
	 g,h := foo3("asd",777)
	 j,k := foo4("asd",777)

	 fmt.Println("c = ",c)
	 fmt.Println("d = ",d,"\nf = ",f)
	 fmt.Println("g = ",g,"\nh = ",h)
	 fmt.Println("j = ",j,"\nk = ",k)
}
```
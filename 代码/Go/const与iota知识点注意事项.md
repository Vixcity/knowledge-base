```go
package main

import "fmt"

// const 可以来定义枚举类型
const(
	// 可以在const小括号中添加关键字iota，每行的iota都会累加1
	// iota只能在const里面使用
	BEIJING=iota // iota = 0
	SHANGHAI // iota = 1
	GUANGZHOU // iota = 2
)

const(
	 a=10*iota
	 b
	 c
)

const(
	 aa,bb=iota+1,iota+2
	 cc,dd
	 ee,ff
	 gg,hh=iota*2,iota*3
	 ii,jj
)

func main()  {
	// 常量只读属性
	const length int = 10 
	fmt.Println("length = ",length)
	// length = 100

	fmt.Println("BEIJING = ",BEIJING)
	fmt.Println("SHANGHAI = ",SHANGHAI)
	fmt.Println("GUANGZHOU = ",GUANGZHOU)

	fmt.Println("a = ",a)
	fmt.Println("b = ",b)
	fmt.Println("c = ",c)

	fmt.Println("aa = ",aa)
	fmt.Println("bb = ",bb)
	fmt.Println("cc = ",cc)
	fmt.Println("dd = ",dd)
	fmt.Println("ee = ",ee)
	fmt.Println("ff = ",ff)
	fmt.Println("gg = ",gg)
	fmt.Println("hh = ",hh)
	fmt.Println("ii = ",ii)
	fmt.Println("jj = ",jj)
}
```
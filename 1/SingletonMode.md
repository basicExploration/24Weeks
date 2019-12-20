# 单例模式

## 概念

单例创建设计模式将类型的实例化限制为单个对象。

也就是说new实例对象的时候只能new一次。

## 代码



```go
package main

import (
	"fmt"
	"sync"
)

var once sync.Once //一个全局的once对象。意味着Do来自一个对象。

type Single struct {
	A int
	B string
}

func NewSingle(a int, b string) *Single {
	var sin *Single
	once.Do(func() {
		sin = &Single{
			A: a,
			B: b,
		}
	})
	return sin
}

func main() {
	fmt.Println(NewSingle(1, "12"))
	fmt.Println(NewSingle(1, "12"))
}


```

output:

```bash
执行完成，耗时：0 ms
&{1 12}
<nil>
```

从结果上可以非常明显的看出来，第二次无法再执行这个new的任务了，因为一个once对象下面的Do只能执行一次。所以第二次的结果就是nil无法执行。

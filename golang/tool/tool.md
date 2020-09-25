## 续 《60亿次for循环,原来这么多东西》Golang 版测试

### 写在前面

看到这篇文章 [60亿次for循环,原来这么多东西](https://segmentfault.com/a/1190000023825766)之后，尝试使用 Golang 看一下效果。

测试 go 版本 `go version go1.14.7 darwin/amd64`

### 尝试重现

1、编写一段 `golang` 代码

```go
package main

import (
	"fmt"
	"log"
	"net/http"
	"runtime"
)

func ServeHTTP(w http.ResponseWriter, r *http.Request) {
	var num uint64 = 0
	for i := 0; i < 5900000000; i++ {
		num += uint64(i)
	}
	fmt.Fprintf(w, "Hello %d\n", num)
	fmt.Fprintf(w, "Goroutine numbers %d\n", runtime.NumGoroutine())
}

func main() {
	http.HandleFunc("/", ServeHTTP)

	log.Fatal(http.ListenAndServe(":8888", nil))
}

```

2、`go run main.go` 启动服务，用 `time curl` 调用这个接口，首次需要`2.961s` 

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gidbwhnsxvj30nd07ednh.jpg)

3、多次调用后，问题未重现

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gidfx7snj0j30nd0iqhc1.jpg)

4、将 59 亿级继续加大，最后加大到 **100 亿**，耗时保持在 4.940s 左右，问题仍未重现

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1gidggwqvtvj30nt0eh7ou.jpg)


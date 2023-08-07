# GoWeb学习笔记

- [GoWeb学习笔记](#goweb学习笔记)
  - [`Handler`接口与`Handler`方法区分](#handler接口与handler方法区分)
  - [`go mod tidy` 与 `go get .`](#go-mod-tidy-与-go-get-)
  - [`go-micro` 框架](#go-micro-框架)
  - [`go struce` 是否可比较](#go-struce-是否可比较)

## `Handler`接口与`Handler`方法区分

- ```Go
  type Handler interface {
    ServeHTTP(ResponseWriter, *Request)  // 路由实现器
  }
  ```
  
  为`Handler`接口

- ```Go

  func (mux *ServeMux) Handler(r *Request) (h Handler, pattern string) {
    if r.Method != "CONNECT" {
      if p := cleanPath(r.URL.Path); p != r.URL.Path {
        _, pattern = mux.handler(r.Host, p)
        return RedirectHandler(p, StatusMovedPermanently), pattern
      }
    }	
    return mux.handler(r.Host, r.URL.Path)
  }

  func (mux *ServeMux) handler(host, path string) (h Handler, pattern string) {
    mux.mu.RLock()
    defer mux.mu.RUnlock()

    // Host-specific pattern takes precedence over generic ones
    if mux.hosts {
      h, pattern = mux.match(host + path)
    }
    if h == nil {
      h, pattern = mux.match(path)
    }
    if h == nil {
      h, pattern = NotFoundHandler(), ""
    }
    return
  }
  ```

  为`ServeMux`的`Handler`方法。注意其中区别

## `go mod tidy` 与 `go get .`

- `go mod tidy` 会调用 `go get` 更新当前 `dependencies`， 包括去除不用的包依赖以及添加新的包依赖，`go get` 可以指定添加固定依赖，存储在 `go.mod` 文件中，同时生成 `go.sum` 用于验证包完整性。

## `go-micro` 框架

- 当服务端返回的 `err` 不为 `nil` 时，返回的所有的 `response` 内的所有数据会丢失，需要注意，尚未调查 `gRPC` 是否是同样机制。 解决方案：服务端 `err` 统一返回 `nil`，错误码及错误信息由 `response` 携带。

- `go-micro` 与 `gRPC` 语法区别

  - 服务端函数将 `response` 封装进了函数参数中，使用指针返回

## `go struce` 是否可比较

- golang 中，所有空结构体的地址一致，为 `runtime.zerobase`，这是在**编译期**就可以确定的。
- golang 结构体之间可比较的前提：
  - 结构体中不能含有 `slice, map` 等不可比较的成员(可以通过 `reflect.DeepEqual` 比较其中含有的 slice 与 map)
  - 结构体内所有变量的顺序，值均需要相等时，结构体才相等
  - 结构体类型需要一致，不一致时需要通过强制转换来判断是否相等
  ```golang
  a := D{}
  b := F{}
  var s struct{}

  fmt.Println(unsafe.Sizeof(a))
  fmt.Println(unsafe.Sizeof(b))
  fmt.Println(unsafe.Sizeof(s))

  fmt.Printf("%p\n", &a)
  fmt.Printf("%p\n", &b)
  fmt.Printf("%p\n", &s)

  mapping := map[interface{}]string{
    interface{}(a): "1",
    interface{}(b): "2",
  }

  for k, v := range mapping {
    if k == F(a) {
      fmt.Println("match a", v)
    }
    if k == b {
      fmt.Println("match b", v)
    }
  }

  Result:
  0
  0
  0
  0x1051c8fc0
  0x1051c8fc0
  0x1051c8fc0
  match a 2
  match b 2
  ```
- 空结构体的使用场景：
  - `map` 和 `struct{}` 结合：不关心 value 只关心快速查询 key 是否存在
    ```golang
    // 创建 map
    m := make(map[int]struct{})
    // 赋值
    m[1] = struct{}{}
    // 判断 key 键存不存在
    _, ok := m[1]
    ```
  - `chan` 和 `struct{}` 结合。用于信号同步，不关心信号中的值
  - `go micro` 中使用空结构体 `type metaKey struct{}` 作为 metadata 的键
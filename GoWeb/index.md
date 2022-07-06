# GoWeb学习笔记


- [GoWeb学习笔记](#goweb学习笔记)
  - [`Handler`接口与`Handler`方法区分](#handler接口与handler方法区分)
  - [`go mod tidy` 与 `go get .`](#go-mod-tidy-与-go-get-)

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
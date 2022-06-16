# GoWeb学习笔记


- [GoWeb学习笔记](#goweb学习笔记)
  - [`Handler`接口与`Handler`方法区分](#handler接口与handler方法区分)

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
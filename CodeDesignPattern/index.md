# 代码设计方法

## 状态机流转
当一个状态流程满足以下几个条件时，可以使用 `Switch case` 的方法提高代码复用率及逻辑可读性

- 状态流转不依赖外部信号的触发
- 流程中调用的所有函数以及下游接口均幂等
- 每个状态下的动作均是固定的

```golang
func TranferStateMachineByCurrentStatus {
  switch {
    case Status1:
      ...
      fallthrough

    case Status2:
      ...
      fallthrough
    
    case EndStatus:
      ...
    
    default:
      panic("unknown status")
  }
}
```

## 锁的使用
为了防止某个函数的执行过程并发，通常需要再该函数调用时增加锁。
- 如果该锁加在了函数内部开头，则此函数的输入参数应具有**不可变的属性**，即其参数不应随着函数过程而变化。

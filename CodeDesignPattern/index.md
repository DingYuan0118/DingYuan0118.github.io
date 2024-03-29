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
- 如果该锁加在了函数内部开头，且函数的输入参数具有**不可变的属性**，则函数的锁有效。
- 如果该锁加在了函数内部开头，并且参数不具备不可变属性，但是却不影响函数流程，则该函数的锁有效

## 流程切分
当一个流程因特殊原因被切分成两个部分时，需要考虑在两部分中间中断时能否有效重试，需要依赖一下两个特性
- 依赖上游状态机可靠重试
- 依赖本函数幂等调用
- 依赖下游幂等

## 接口设计考虑
- 幂等性
- 并发性

## 流程设计考虑
当一个流程涉及多个交互方时理论上要注意一下几点
- 当涉及交互方有调用写接口时，每一个写操作可以添加一个状态，同时需要下游幂等
- 状态机的推进需要考虑可重试性
- 当一条记录设计多个流程的写操作时，需要加底层的 sql 行锁，或需要从流程中加锁保证临界区
- 调用三方接口时需要考虑成功、失败两种业务正常情况处理以及超市等异常情况处理。超时通常panic等待重试
- 修改流程时需要考虑旧数据的兼容问题
- 新流程设计时需要考虑异常数据的问题，通常有字符->int, 空字符，非法字符，大小写等问题

## 自驱状态机设计
- 当一个状态机转换无需外部信号，可以自发推进至终态时，可以设计一个 `自驱状态机`，通过将状态与每个状态下固定的动作绑定，能够很好模拟实现预先设计的状态机转换推进。
- 状态机结构设计如下
  ![Alt text](image.png)

- 提供 Drive 方法自主推进或重试推进状态
  ```golang
  // Drive 自驱推进至终态
  func (s *SelfDriveStateMachineImpl) Drive(ctx context.Context, params interface{}) {
      if s.curState == nil {
          panic(fmt.Sprintf("CurState is nil. Please set curstate."))
      }
      for !s.curState.IsFinal() {
          s.Next(ctx, params)
      }
  }
  ```

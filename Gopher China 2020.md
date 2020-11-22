[TOC]

# Gopher China 2020

## Tantan 分布式存储

### 自研 tantan db

- 日常场景： 擦肩而过，左划右划喜欢不喜欢。

- 数据类型： 日 万亿
- 时间要求： 低 
- 需求：
  - 分布式(主从)，可用，可拓展（拓展无感），定制化。
  - 可支持SQL， ACID
  - 存储成本，运维友好，容灾能力。
- 对比开源
  - Nosql： sql限制，存储成本(编码问题，存储的是类似BJSON？JSON等字符串)，定制化问题
  - NewSql(是什么？): 存储成本， 强一致性（其实只需要弱一致性），延迟，定制化
- 使用map, reduce方法进行数据的分片
  - 存在的问题：扩容
- 高可用
  - 心跳检测机制
- why golang
  - 标准库
  - goroutine
    - Https://github.com/uber-go/goleak
    - runtime.NumGoroutine()
    - Pprof
  - gc
    - 减少对象分配
      - 复用 会造成内存泄漏，如果对象池过大，可能会导致内存泄漏
      - 预分配



- Q&A
  - 如何判断

## Go Programming Patterns

- 可配置参数实现
  - 通过内嵌Config结构来完成初始化
  - functional options
    - 定义一个Options 函数体， 可以在初始化的时候统一传入一个 **options ...** 
- Map/Reduce
  - 通过传入函数来自定义
- decorator
  - 通过函数包装来修饰，传入一个函数，然后修饰它，在返回一个函数。
- 总体的思想感觉就是函数式编程。



## GO In GrabFood Discovery System

搜索与推荐

- 内部的rpc 框架 grab-kit
  - 特点：可维护，伸缩，容错，观察。
  - 默认http/rpc传输协议
  - 自动生成代码
  - 丰富的middleware
  - 第三方平台的高度集成(?)





## 华为云

- 微服务信息存储：静态信息 + 动态信息
  - 静态：ip，服务等
  - 动态：实例信息
- API管理(契约管理)
  - 方便管理
  - 提前交互方便测试
  - 防治糟糕的api风格
  - 防止相似的api定义能力
- 依赖管理
  - 双向依赖
  - 循环依赖
  - 调用深度
- 缓存机制
  - 服务中心会将所有的数据
  - etcd
- 插件功能
  - ciphet
  - id gen
  - metrics
  - alarm
  - log ...
- 存储
  - etcd
  - mongodb
- 插件化的实现可以提高服务的可用性，方便用户实现定制化的功能。达到一个可插拔的效果。



## Functional options and config for APIs

### 可选参数与必选参数

#### 场景

- 需要自定义timeout
- 需要密码
- 需要策略
- 各种定制化的需求

#### 诉求

减少API的表面积

#### 解决方案

##### Configuration struct API

- **无法确定可选与必选参数**， 除非用文档进行说明
- 无法确定某些参数的修改会产生的副作用

##### Functional options

- ```go
  func dial(nework, address string, options... *DialOption) (Conn, error){
  	...
    for_, option := range options {
      option(&...)
    }
  }
  ```

- 可能遇到的问题
  - 配置文件的加载比较的麻烦
  - 将配置文件转换成一种结构体(other -> json -> struct)，然后提供一个函数来获取`options`




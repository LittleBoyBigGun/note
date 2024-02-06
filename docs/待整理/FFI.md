FFI 全称 Foreign Function Interface ， 是用来与其它语言交互的接口， 在有些语言里面称为语言绑定， java 里边一般称为 JNI。

跨语言进程调用通常有两种方式

- FFI
- 把函数做出一个服务， 通过 IPC 或者 网络(http, RPC, RESTful 等来交换数据)
## 这是一个HTTP服务器


### Version-1.4.2

- C语言开发
- `epoll` + 类线程池技术
- `Linux` 环境， 非跨平台
- 使用`CMake-3.3`。 可以直接修改版本号搭配自己平台的使用，当前`apt`上的版本为`2.8`
- 英文注释

### 测试使用
- 在`/etc/wushxin/wsx.conf`中写好配置后，进入源文件路径
- `$ cmake .`
- `$ make`
- `$ ./httpd3`
- 配置参见下方说明

### 模块
- 最外部为入口程序，以及读取配置的函数。
- `handle` 模块则是对于 **读/写/错误** 事件的一个控制
- `memop`模块是用来扩展内存分配的，例如`jcmalloc`，目前只是使用自带的库函数，并加一层包装。
- `config` 暂时存放配置文件
- `timer` 模块(待开发，未添加)，用于无效`socket`的关闭回收
- `util` 模块为待开发模块，当前拥有 `string_t` 类型

### 配置文件
- 可以在 `config` 文件夹下的 `wsx.conf` 参考详细配置格式，当前支持的配置选项只有四个
	- 详见配置文件
	- 以 `#` 开头的为注释，单行有效

- 配置文件放在 `"./wsx.conf", "/etc/wushxin/wsx.conf"`这两个地方，配置文件名字不能改变。

### 编译参数
- `gcc -std=gnu99 -pthread -O3`
- `-DWSX_BASE_DEBUG` 是两个基础输出，在`handle.c`中
- `-DWSX_DEBUG_ALL` 是剩下的所有输出

### 测试
- **单核双线程,8G内存**的虚拟机配置中(笔记本)
	- 在开启缓存时，对**64K**文件的请求，是`5500~8000 Request/Sec`(测试脚本干扰下)
	- 不开启缓存时，对**64K**文件的请求，是`2600+Request/Sec`(测试脚本干扰下)
![](http://ww1.sinaimg.cn/mw690/81b736ebjw1f2rz7lq9l0j20bc068t9n.jpg)


> 缓存的意思是，将文件映射到内存中，且只映射一次，不重复打开映射，关闭映射

> 不开启缓存的意思是，每次请求资源都 **open, mmap, close, unmmap**

### 进度
- 完成总体程序框架的编写，基本功能完成
- 使用新数据结构 `String` 解决发送文件过大问题。
- 实现`GET HEAD`

### TODO
- `timer`模块
- `util`模块
	- 实现一个底层 `SkipList` 去存储和维护客户连接，减少内存占用
	- 实现一个 `HashMap` 去存储热点文件名，并将热点文件持续地在内存中打开，即缓存
- 能处理更多的属性
- 添加缓存支持
- **http** 请求头 : `POST`

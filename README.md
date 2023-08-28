# WebServer
  使用同步 I/O 方式模拟出 Proactor 模式。原理是：主线程执行数据读写操作，读写完成之后，主线程向工作线程通知这一”完成事件“。那么从工作线程的角度来看，它们就直接获得了数据读写的结果，接下来要做的只是对读写的结果进行逻辑处理。
  使用同步 I/O 模型（以 epoll_wait为例）模拟出的 Proactor 模式的工作流程如下：
    1. 主线程往 epoll 内核事件表中注册 socket 上的读就绪事件。
    2. 主线程调用 epoll_wait 等待 socket 上有数据可读。
    3. 当 socket 上有数据可读时，epoll_wait 通知主线程。主线程从 socket 循环读取数据，直到没有更多数据可读，然后将读取到的数据封装成一个请求对象并插入请求队列。
    4. 睡眠在请求队列上的某个工作线程被唤醒，它获得请求对象并处理客户请求，然后往 epoll 内核事件表中注册 socket 上的写就绪事件。
    5. 主线程调用 epoll_wait 等待 socket 可写。
    6. 当 socket 可写时，epoll_wait 通知主线程。主线程往 socket 上写入服务器处理客户请求的结果。
  同步 I/O 模拟 Proactor 模式的工作流程：
  ![image text](https://github.com/err4ntry/WebServer/blob/master/1693219334326.jpg "DBSCAN Performance Comparison")

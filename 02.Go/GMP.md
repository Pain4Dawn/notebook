# GMP概述
## GMP解释
+ G goroutine，分为本地和全局
+ P 处理器，P的本地队列包含G，通过GOMAXPROCS
+ M M运行任务，获得P中的G执行，一个M阻塞了就会创建一个M
## 线程复用
+ work stealing复用：当本地线程无运行G时，尝试从其他线程绑定的P偷取G，而不是销毁线程。
+ hand off机制：当本线程因为 G 进行系统调用阻塞时，线程释放绑定的 P，把 P 转移给其他空闲的线程执行。
+ 利用并行：最多线程核数/2
+ 抢占：gorouting最多抢占CPU10ms，防止其他goroutine被饿死。
+ 全局G：当偷不到G时，会获取全局G

[GMP深入浅出](https://learnku.com/articles/41728)
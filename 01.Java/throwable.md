# throwable

+ Java处理错误的类体系，本身的类型信息，根据不同的类描述错误内容
+ throwable分为错误和Exception，错误不可以被捕获，程序终止
+ Exception分为RuntimeException和其他类型的Exception。RuntimeException 可以不用捕获，其他必须捕获
+ RuntimeException有 NullPointerException、IllegalArgumentException
+ 抛出异常，打印出来直接是堆栈信息，无论在哪个函数打印
+ 抛出异常，即使是父类也会打印详细的错误类型，比如io异常等
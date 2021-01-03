### ProducerAPI

1. 消息发送流程

    kafka的producer发送消息采用的是异步发送的方式。在消息发送的过程中，涉及到了两个线程--main线程和sender线程，以及一个线程共享变量--RecordAccumulator，main线程将消息发送给RecordAccumulator，sender线程不断从RecordAccumulator中拉取消息发送到kafka broker

    <img src="upload\image-20201226155149111.png" alt="image-20201226155149111" style="zoom:33%;" />


### ConsumerAPI

### 自定义Interceptor

1. 拦截器原理

    ​	interceptor是在kafka0.10版本被引入的，主要用于实现clients端的定制化控制逻辑

    ​	对于producer而言，interceptor使得用户在消息发送前以及producer回调逻辑前有机会对消息做一些定制化需求，比如修改消息等。同时，producer允许用户指定多个interceptor按序作用于同一条消息从而形成一个拦截链（interceptor chain），interceptor的实现接口是org.apache.kafka.clients.producer.ProducerInterceptor，其定义的方法包括：

    1. configure（congifs）

        获取配置信息和初始化数据时调用

    2. onSend（ProducerRecord）

        ​	该方法封装进KafkaProducer.send方法中，即他运行在用户主线程中。Producer确保在消息被序列化以及计算分区前调用该方法。用户可以再该方法中对消息做任何操作，但最好保证不要修改消息所属的topic和分区，否则会影响目标分区的计算

    3. onAcknowledgement(RecordMetadata,Exception)

        ​	该方法会在消息从RecordAccumulator成功发送到kafka Broker之后，或者在发送过程中失败时调用。并且通常都是在producer回调逻辑出发之前，onAcknowledgement运行在producer的IO线程中，因此不要在该方法中放入很重的逻辑，否则会拖慢producer的消息发送效率

    4. close

        ​	关闭interceptor，主要用于执行一些资源清理工作

        ​	如前所述，interceptor可能被运行在多个线程中，因此在具体实现时用户需要自行确保线程安全。另外倘若指定了多个interceptor，则producer将按照指定顺序调用他们，并仅仅是捕获每个interceptor可能抛出的异常记录到错误日志中而非向上传递。这在使用过程中要特别留意

2. 
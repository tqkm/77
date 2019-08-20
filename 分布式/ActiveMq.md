ActiveMq
---
ActiveMq完全基于jms规范开发的，纯java语言编写，有两种模式，点对点以及发布订阅模式；点对点模式队列可以有多个消费者，每个消息只能被**一个**消费者消费，
若是没有消费者在线，可以等到消费者上线再消费；发布订阅模式中每个队列可以有多个消费者，每个消息可以被多个消费者消费，当为非持久订阅时，消费者不在线时无法消费消息，持久订阅时，订阅了topic后可在上线后消费不在线的那些信息<br>

activemq消息发送可分为同步发送和异步发送，同步发送在生产者发送数据到broker后，会阻塞直到broker返回ack。异步发送中生产者发送数据到broker无需等待
broker返回确认，性能较高，但会出现消息丢失的情况。<br>
所有的事务会话，在没有commit之前，不会有ack，在commit之后，才有ack，此过程中可以进行批量ack<br>
非事务性会话，可以设置自动ack确认，也可以设置手动ack确认，批量ack确认<br>
acktype:<br>
DELIVERED_ACK_TYPE：通知broker已经消息已经投递到消费者，但还没做处理<br>
STANDARD_ACK_TYPE：通知broker消息处理成功 <br>
REDELIVERED_ACK_TYPE：通知broker消息重发 <br>
INDIVIDUAL_ACK_TYPE：表示手动确认时，只确认单条消息 <br>
UNMATCHED_ACK_TYPE ：表示Topic中，如果一条消息在转发给“订阅者”时，发现此消息不符合Selector过滤条件，那么此消息将 不会转发给订阅者，消息将会被存储引擎删除(相当于在Broker上确认了消息)<br>
POSION_ACK_TYPE ：消息”错误”,通常表示”抛弃”此有毒消息，比如消息重发多次后，都无法正确处理时，消息将会被删除或者DLQ(死信队列)<br>


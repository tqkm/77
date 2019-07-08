Springcloud
---
eureka：服务发现与注册<br>
config：配置中心<br>
feign：声明式http客户端<br>
hystrix：断路器<br>
gateway：服务网关<br>

配置文件说明：
~~~
eureka服务端配置文件
server:
  port: 8761  //端口号
spring:
  application:
    name: eurekaserver  //注册到服务上的名字
  profiles:
    active: dev   //读取application-dev.yml的文件
  cloud:
    inetutils:
      preferred-networks: 127.0.0.1
    client:
      ip-address: 127.0.0.1  //指定实例ip地址
eureka:
  server:
    peer-node-read-timeout-ms: 3000 //超时时间
    enable-self-preservation: true  //是否开启自我保护，默认为true，如果为true，服务中心丢失客户端后，并不会将客户端剔除，可能会造成问题
  instance:
    prefer-ip-address: true  //是否以ip注册到服务中心，默认是以hostname注册
    instance-id: ${spring.cloud.client.ip-address}:${server.port}
  client:
    registerWithEureka: true
    fetchRegistry: false
    healthcheck:
      enabled: true
    serviceUrl:
      defaultZone: http://127.0.0.1:8761/eureka/  //注册中心默认地址
~~~

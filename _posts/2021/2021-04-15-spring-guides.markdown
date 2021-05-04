---
layout: post
title:  "spring-guides"
category: spring
date:   2021-04-28 21:25:35 +0200
---

## 1、构建一个RESTFul接口
[rest-service](https://spring.io/guides/gs/rest-service/)

要点：引入web依赖，web自带jackson依赖，spring支持json数据的反转（使用jackson2），反转实现类（MappingJackson2HttpMessageConverter ） @RestController是一个组合注解类
> @SpringBootApplication is a convenience annotation that adds all of the following:

>> @Configuration: Tags the class as a source of bean definitions for the application context.

>> @EnableAutoConfiguration: Tells Spring Boot to start adding beans based on classpath settings, other beans, and various property settings. For example, if spring-webmvc is on the classpath, this annotation flags the application as a web application and activates key behaviors, such as setting up a DispatcherServlet.

>> @ComponentScan: Tells Spring to look for other components, configurations, and services in the com/example package, letting it find the controllers.

## 2、消费一个RESTFul接口 
[consuming-rest](https://spring.io/guides/gs/consuming-rest/)
> 使用spring resttemplate取回对应的数据，创建对应的领域类，领域类的字段和rest接口返回的字段不一样的时候，使用@JsonProperty注解注释字段；当接口返回字段多的时候，使用@JsonIgnoreProperties(ignoreUnknown = true)注释类，自动忽略多的字段。

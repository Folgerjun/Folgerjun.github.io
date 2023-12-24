---
title: 事务嵌套使用注意点
date: 2021-03-06 20:10:55
categories: [开发,总结]
tags: [事务]
---

最近在项目中需要用到事务嵌套使用，即方法 A 中调用方法 B，A 和 B 都有事务控制，且方法 B 的事务独立于 A，为一个独立新事务。

刚开始用默认的事务类型 `PROPAGATION_REQUIRED`，结果当然是失败了。后来查找了事务的类型以及事务传播机制，简单列一下如下：


- `PROPAGATION_REQUIRED`	表示如果当前事务存在，则支持当前事务。否则，会启动一个新的事务。默认的事务类型。
- `PROPAGATION_SUPPORTS`	表示如果当前事务存在，则支持当前事务，如果当前没有事务，就以非事务方式执行。
- `PROPAGATION_MANDATORY`	表示如果当前事务存在，则支持当前事务，如果当前没有事务，则返回事务嵌套错误。
- `PROPAGATION_REQUIRES_NEW`	表示新建一个全新 Session 开启一个全新事务，如果当前存在事务，则把当前事务挂起。
- `PROPAGATION_NOT_SUPPORTED`	表示以非事务方式执行操作，如果当前存在事务，则新建一个Session以非事务方式执行操作，把当前事务挂起。
- `PROPAGATION_NEVER`	表示以非事务方式执行操作，如果当前存在事务，则返回事务嵌套错误。
- `PROPAGATION_NESTED`	表示如果当前事务存在，则在嵌套事务内执行，如嵌套事务回滚，则只会在嵌套事务内回滚，不会影响当前事务。如果当前没有事务，则进行与 PROPAGATION_REQUIRED 类似的操作。
- `PROPAGATION_NOT_REQUIRED`	表示如果当前没有事务，就新建一个事务，否则返回错误。


了解了事务类型以及其传播机制之后，结合业务我当然选择了 `PROPAGATION_REQUIRES_NEW`。

当我以为一切就将迎刃而解之时，debug...，what！！为什么没有生效！！

然后我又在知识海洋中继续搜寻蛛丝马迹，渴求那些曾与我一同有此境遇的可怜人的良方。

最终在几年前的帖子中终于找到了我想要的解决方案：

**方法 B 需要写在另一个类中，不能跟方法 A 写在一个类里面，不然就不会生效！**

详情可看该文章：[spring事务@Transactional在同一个类中的方法调用不生效](https://blog.csdn.net/qq_30336433/article/details/83338835)

简单来说就是： **Spring 采用动态代理(AOP)实现对 bean 的管理和切片，它为我们的每个 class 生成一个代理对象。只有在代理对象之间进行调用时，可以触发切面逻辑。而触发切面逻辑才会进入事务拦截器从而加入到事务管理器中让事务生效！**

记录一下。
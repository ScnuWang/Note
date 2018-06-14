---
title: Mybatis笔记
tags: Mybatis
grammar_cjkPython: true
date:  2018-6-14
---


### 一、作用域和生命周期

	依赖注入框架可以创建线程安全的、基于事务的 SqlSession 和映射器（mapper）并将它们直接注入到你的 bean 中，因此可以直接忽略它们的生命周期。如果对如何通过依赖注入框架来使用 MyBatis 感兴趣可以研究一下 MyBatis-Spring 或 MyBatis-Guice 两个子项目。


1. SqlSessionFactoryBuilder

	一旦创建了 SqlSessionFactory，就不再需要它了。可以创建多个不同的SqlSessionFactory，但是不建议这样做。SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域（也就是局部方法变量）
	
2. SqlSessionFactory

	一旦被创建就应该在应用的运行期间一直存在。SqlSessionFactory 的最佳实践是在应用运行期间不要重复创建多次。最简单的就是使用单例模式或者静态单例模式。SqlSessionFactory 的最佳作用域是应用作用域
	
3. SqlSession

	每个线程都应该有它自己的 SqlSession 实例。换句话说，每次收到的 HTTP 请求，就可以打开一个 SqlSession，返回一个响应，就关闭它。最佳的作用域是请求或方法作用域
	
4. Mapper Instances

	映射器接口的实例是从 SqlSession 中获得的。因此从技术层面讲，任何映射器实例的最大作用域是和请求它们的 SqlSession 相同的。映射器实例的最佳作用域是方法作用域。
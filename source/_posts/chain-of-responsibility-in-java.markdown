---
layout: post
title: "设计模式：责任链（chain of responsibility） in java"
date: 2012-11-02 20:09
comments: true
categories: 设计模式
tags: [ chain, pattern, 责任链, 设计模式 ]
---
**定义：**使多个对象都有机会处理请求，从而避免了请求的发送者和接受者之间的耦合关系。将这些对象连成一条链，并沿着这条链传递该请求，直到有对象处理它为止。    

责任链由2个角色组成：   

- Handler抽象处理者角色    

  它定义了一个处理请求的接口。当然对于链子的不同实现，也可以在这个角色中实现后继链。    

- Concrete Handler具体处理者角色    

  实现抽象角色中定义的接口，并处理它所负责的请求。如果不能处理则访问它的后继者。  

比如Filter就类似这种，每个Filter都对请求进行处理，如果解决不了，则交给下一个进行处理。    
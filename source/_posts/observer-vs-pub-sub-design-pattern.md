---
title: 观察者模式和发布订阅者模式
tags:
- JavaScript
- Design Pattern
---
虽然是很多时候容易弄混的两个设计模式，但其实还有有一定区别的。

<!-- ## Quick Start -->

### Observer Design Pattern (观察者模式)

<!-- 包含一个**Subject**（主题）或“被观察者”和一个Observer（观察者），观察者直接订阅主题，当主题里面的状态发生变化的时候会自动通知观察者。 -->

其中一个对象(**Subject**)维护其依赖项的列表（称为观察者），并且通常通过其调用方法之一来自动将状态更新告诉他们。

举个例子，比如你正在找一份前端开发的工作，然后联系了一名Google的HR，之后HR也留了你的联系方式并告诉你如果有任何JD会立即通知你。当然HR肯定不止留了你一个人的联系方式，还有很多和你一样的候选人（**Observer**）。一旦有JD出来，HR会通知到所有的候选人。

在以上的例子中，Google就是**Subject**，维护了所有的Observers（像你一样的求职者）, 然后会通知给所有的Observers特定的event（有JD啦）。

![](/images/observer-vs-pub-sub-design-pattern/observer.png)

```javascript
// Observer 观察者
class Developer {
  constructor(name, position) {
    this.name = name;
    this.position = position;
  }
  messageReceiving(position) {
    console.log(`Hi ${this.name}, there is a ${position} position avaliable for you.`);
  }
}

// 用来集中管理开发者
class Developers {
  constructor() {
    this.list = [];
  }
  add(obj) {
    return this.list.push(obj);
  }
  find(index) {
    return this.list[index];
  }
  getCount() {
    return this.list.length;
  }

}

// Subject 主题或者被观察者
class Google {
  constructor() {
    this.developers = new Developers();
  }
  notify(position) {
    const count = this.developers.getCount();
    for (let i = 0; i < count; i++) {
      this.developers.find(i).messageReceiving(position);
    }
  }
  resumeReceving (obj) {
    this.developers.add(obj);
  }
}

const frontEnd = new Developer("Shawn", 'Front-end');
const frontEnd2 = new Developer("Hayden", 'Front-end');
const Google2 = new Google();
Google2.resumeReceving(frontEnd);
Google2.resumeReceving(frontEnd2);
Google2.notify("Front-end");

```

### Pub-sub Design Pattern

在发布订阅者模式中，**Publisher** 和观察者模式中的**Subject**类似，**Subscriber**和观察者模式中的**Observer**几乎是同一个意思。

在发布订阅者模式中，订阅者和发布者不会直接交流，而是通过一个叫**Event Channel**（事件调度中心）来对事件进行统一管理。

这样的好处是可以避免订阅者和发布者产生依赖关系，也就是我们常说的**解耦**。


![](/images/observer-vs-pub-sub-design-pattern/pub-sub.gif)


举个例子：

谷歌(**Publisher**)会把工作发布到一个名叫CEO直聘(**Event Channel**)的工作平台上，开发者也可以在这个平台上找工作，如果他们对Google特别感兴趣，可以直接在这个平台上订阅谷歌的职位消息，一旦谷歌发布了职位，平台就会通知到已经订阅的(**Subscribers**)开发者。


```javascript
// Publisher 发布者
class JobSeeking {
  constructor() {
    this.companies = [];
    this.uid = 0;
  }
  // company 可以看作为 eventType 事件类型
  // 发布
  emit(company, position) {
    let subscribers = this.companies[company];
    if (!subscribers) {
      return false;
    }
    let count = subscribers.length;
    while (count--) {
      let subscriber = subscribers[count];
      subscriber.handler(company, subscriber.id, position)
    }
  }
  // 订阅
  on(company, handler) {
    if (!this.companies[company]) {
      this.companies[company] = [];
    }
    let id = ++this.uid;
    this.companies[company].push({
      id,
      handler
    });
    return id;
  }
}

let manager = new JobSeeking();

manager.on('Google', (company, id, position) => {
  console.log(`Hi No.${id}, ${company} has published a new ${position} posion.`);
});
manager.on('Microsoft', (company, id, position) => {
  console.log(`Hi No.${id}, ${company} has published a new ${position} posion.`);
});
manager.emit('Google', 'Front-end');
manager.emit('Microsoft', 'Front-end');

```

### 两者对比


![](/images/observer-vs-pub-sub-design-pattern/both.jpeg)


观察者模式是紧密耦合，简单直接，扩展性差。
发布订阅者模式松散耦合，两者不产生依赖，易于扩展。


两者有一个非常大的语义区别：发布订阅者模式只关于与**Broadcasting**事件广播，发布者也不需要知道事件最终去了哪里以及谁订阅了这些事件。


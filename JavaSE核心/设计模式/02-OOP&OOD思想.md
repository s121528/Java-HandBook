# 1 面向对象特性

OOA（面向对象分析）、OOD（面向对象设计）、OOP（Object Oriented Programming，面向对象编程），这3个概念，对于我们JAVA程序员来讲，或多或少应该都有所了解，或者说至少都听说过。

POP（面向过程编程）、OOP（面向对象编程）、IOP（面向抽象编程）

## 1.1 抽象

把客观事物进行数据模型的抽象，OO思维，考虑名词，结合实际情况。

## 1.2 封装

封装，也就是把客观事物封装成抽象的类，并且类可以把自己的属性和方法只让可信的类操作，对不可信的进行信息隐藏。

## 1.3 继承

继承是指这样一种能力，它可以使用现有的类的所有功能，并在无需重新编写原来类的情况下对这些功能进行扩展。

## 1.4 多态

多态的**必要条件**：01、要有继承或实现；02、要有方法的重写；03、父类引用指向子类对象。

多态指一个类实例的相同方法在不同情形有不同的表现形式。具体来说就是不同实现类对公共接口有不同的实现方式，但这些操作可以通过相同的方式（公共接口）予以调用。

如果有这个概念，确确实实的有，但是没有具体的东西，可以设计成**抽象类**。

如果是事物的共同特征，可以设计成**接口**。

### 1.4.1 抽象类



### 1.4.2 接口

接口实际生活中最常见的例子就是：插座

定义好插座的**接口标准**，各大插座厂商只要按这个接口标准生产，管你什么牌子、内部什么电路结构，这些均和用户无关，用户拿来就可以用；而且即使插座坏了，只要换一个符合接口标准的新插座，一切照样工作！

# 2 面向对象原则

面向对象设计（OOD）有**_七_**大原则（是的，你没看错，是七大原则，不是六大原则），它们互相补充。

设计模式六大原则——SOLID、

2.1 开-闭原则
-------------------------

Open-Close Principle（OCP），即开-闭原则。开，指的是对**扩展开放**，即要支持方便地扩展；闭，指的是对**修改关闭**，即要严格限制对已有内容的修改。开-闭原则是最抽象也是最重要的OOD原则。[简单工厂模式](http://www.jasongj.com/design_pattern/simple_factory/)、[工厂方法模式](http://www.jasongj.com/design_pattern/factory_method/)、[抽象工厂模式](http://www.jasongj.com/design_pattern/abstract_factory/)、bean工厂中都提到了如何通过良好的设计遵循开-闭原则。

```properties
01、定义：一个软件实体如类、模块和函数应该对扩展开放，对修改关闭。
02、用抽象构建框架，用实现扩展细节
03、优点：提高软件系统的可复用性及可维护性
```

## 2.2 依赖倒置原则

Dependence Inversion Principle（DIP），依赖倒置原则。它讲的是 “设计和实现要依赖于抽象而非具体”。一方面抽象化更符合人的思维习惯；另一方面，根据里氏替换原则，可以很容易将原来的抽象替换为扩展后的具体，这样可以很好的支持开-闭原则。

```properties
01、定义：高层模块不应该依赖低层模块，二者都应该依赖其抽象
02、抽象不应该依赖细节；细节应该依赖抽象
03、针对接口编程，不要针对实现编程
04、优点：可以减少类间的耦合性、提高系统稳定性，提高代码可读性和可维护性，可降低修改程序所造成的风险。
```

## 2.3 里氏替换原则

Liskov Substitution Principle（LSP），即里氏替换原则。该原则规定 “子类必须能够替换其父类，否则不应当设计为其子类”。换句话说，父类出现的地方，都应该能由其子类代替。所以，子类只能去扩展基类（父类），而不是隐藏或者覆盖基类。

2.4 单一职责原则
--------------------------

Single Responsibility Principle（SRP），单一职责原则。它讲的是不要存在多于一个导致类变更的原因，是高内聚低耦合的一个体现。

```properties
01、定义：不要存在多于一个导致类变更的原因。
02、一个类/接口/方法只负责一项职责。
03、优点：降低类的复杂度、提高类的可读性，提高系统的可维护性、降低变更引起的风险。
```

## 2.5 接口隔离原则

Interface Segration Principle（ISP），接口隔离原则，“将大的接口打散成多个小的独立的接口”。由于 Java 类支持实现多个接口，可以很容易的让类具有多种接口的特征，同时每个类可以选择性地只实现目标接口。

```properties
01、定义：用多个专门的接口，而不使用单一的总接口，客户端不应该依赖它不需要的接口。
02、一个类对一个类的依赖应该建立在最小的接口上。
03、建立单一接口，不要建立庞大臃肿的接口，尽量细化接口，接口中的方法尽量少。
04、注意适度原则，一定要适度。
05、优点：符合我们常说的高內聚低耦合的设计思想,从而使得类具有很好的可读性、可扩展性和可维护性。
```

## 2.6 迪米特法则

Law of Demeter or Least Knowledge Principle（LoD or LKP），迪米特法则或最少知道原则。它讲的是 “一个对象就尽可能少的去了解其它对象”，从而实现松耦合。如果一个类的职责过多，由于多个职责耦合在了一起，任何一个职责的变更都可能引起其它职责的问题，严重影响了代码的可维护性和可重用性。

```properties
01、定义：一个对象应该对其他对象保持最少的了解，又叫最少知道原则。
02、尽量降低类与类之间的耦合。
03、降低类之间的耦合。
04、强调只和朋友交流，不和陌生人说话。
05、朋友：出现在成员变量、方法的输入、输出参数中的类称为成员朋友类，而出现在方法体内部的类不属于朋友类。
```

2.7 合成复用原则
-------------------------------------

Composite/Aggregate Reuse Principle（CARP/CRP），合成 / 聚合复用原则。如果新对象的某些功能在别的已经创建好的对象里面已经实现，那么应当尽量使用别的对象提供的功能，使之成为新对象的一部分，而不要再重新创建。新对象可通过向这些对象的委派达到复用已有功能的效果。简而言之，要尽量使用合成/聚合，而非使用继承。《[Java 设计模式（九） 桥接模式](http://www.jasongj.com/design_pattern/bridge/)》中介绍的桥接模式即是对这一原则的典型应用。

# 3 设计模式

3.1 什么是设计模式
-----------------------------

可以用一句话概括设计模式———设计模式是一种利用 OOP 的封闭、继承和多态三大特性，同时在遵循单一职责原则、开闭原则、里氏替换原则、迪米特法则、依赖倒置原则、接口隔离原则及合成 / 聚合复用原则的前提下，被总结出来的经过反复实践并被多数人知晓且经过分类和设计的可重用的软件设计方式。

## 3.2 设计模式分类

### 3.2.1 创建型

> 工厂方法模式、抽象工厂模式、建造者模式、单例模式、原型模式。

### 3.2.2 结构型

> 外观模式、装饰者模式、适配器模式、亨元模式、组合模式、桥接模式、代理模式。

### 3.2.3 行为型

> 策略模式、观察者模式、责任链模式、备忘录模式、模板方法模式、迭代器模式、中介者模式、命令模式、访问者模式、解释器模式、状态模式。

3.3 设计模式十万个为什么
--------------------------------------

### 3.3.1 为什么要用设计模式

*   设计模式是高级软件工程师和架构师面试基本必问的项目（先通过面试进入这个门槛我们再谈其它）
*   设计模式是经过大量实践检验的安全高效可复用的解决方案。不要重复发明轮子，而且大多数时候你发明的轮子还没有已有的好
*   设计模式是被主流工程师 / 架构师所广泛接受和使用的，你使用它，方便与别人沟通，也方便别人code review（这个够实在吧）
*   使用设计模式可以帮你快速解决 80% 的代码设计问题，从而让你更专注于业务本身
*   设计模式本身是对几大特性的利用和对几大设计原则的践行，代码量积累到一定程度，你会发现你已经或多或少的在使用某些设计模式了
*   架构师或者 team leader 教授初级工程师设计模式，可以很方便的以大家认可以方式提高初级工程师的代码设计水平，从而有利于提高团队工程实力

### 3.3.2 要尽可能使用设计模式

**是不是一定要尽可能使用设计模式**。每个设计模式都有其适合范围，并解决特定问题。所以项目实践中应该针对特定使用场景选用合适的设计模式，如果某些场景下现在的设计模式都不能很完全的解决问题，那也不必拘泥于设计模式本身。实际上，学习和使用设计模式本身并不是目的，目的是通过学习和使用它，强化面向对象设计思路并用合适的方法解决工程问题。

### 3.3 3 设计模式有时并非最优解

有些人认为，在某些特定场景下，设计模式并非最优方案，而自己的解决方案可能会更好。这个问题得分两个方面来讨论：一方面，如上文所述，所有设计模式都有其适用场景，“one size does not fit all”；另一方面，确实有可能自己设计的方案比设计模式更适合，但这并不影响你学习并使用设计模式，因为设计模式经过大量实战检验能在绝大多数情况下提供良好方案。

### 3.3.4 设计模式太教条化

设计模式虽然都有其相对固定的实现方式，但是它的精髓是利用 OOP 的三大特性，遵循 OOD 七大原则解决工程问题。所以学习设计模式的目的不是学习设计模式的固定实现方式本身，而是其思想。

### 3.3.5 引导团队学习设计模式

**我有自己的一套思路，没必要引导团队成员学习设计模式。**设计模式是被广泛接受和使用的，引导团队成员使用设计模式可以减少沟通成本，而更专注于业务本身。也许你有自己的一套思路，但是你怎么能保证团队成员一定认可你的思路，进而将你的思路贯彻实施呢？统一使用设计模式能让团队只使用20%的精力决解80%的问题。其它20%的问题，才是你需要花精力解决的。

# 4 尽情设计

## 4.1 认识

设计没有绝对的对与错，Over Design也是一种罪过，没有任何实际中的设计会一步到位，初学者不要考虑太多的原则和条条框框,最重要是动手写。
抽象类与接口
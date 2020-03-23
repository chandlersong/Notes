## 画界面

一年前学习过。现在重新学起来，发觉已经忘记的差不多了。但是反而把事情看的简单很多。

- 所有东西，都必须代码来写。然后通过一些布局管理器的逻辑给弄上去。
- 默认所有的widget都是stateless的。就是不会变换的。
- 变换的widget，本质来说，就是外面包层皮这个是**静态的**
    - 组件在**state**类里面组装。这个**动态的**
    - 外面的皮，读取内部的组件，这个是**静态的**

- [官方文章](https://flutter.dev/docs/development/data-and-backend/state-mgmt/declarative)：和我想的一样。本质来说，就是通过`State`来画`UI`。改变state，就改变UI
    - [一篇文章，我看的还是能够看懂](https://hongruqi.github.io/2019/01/26/Flutter%20%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3%20State/)
    - 文中手state是一种行为。我觉得更像是一种变化的数据集合。Flutter的架构，通过不断的获取数据集合，来**重画**widget
    - 里面介绍了几个生命周期，比如build，和initial。
    -  `didChangeDependencies`: 触发监听器？就是说在调用前？
    -  `setState`:会调用`build`方法，上层需要画下层的时候也需要。从这里就可以感觉到，其实本质，还是一个**框架要参数**的过程。

## 导航

[官方文档](https://flutter.dev/docs/development/ui/navigation)：这里讲的还是比较详细的。大致来说就是两条路。

### pop和push

其实做过app的开发的都明白，很多page本质，还是page的栈。这里就入栈和出栈。  

push就是入栈。也就是显示画面。  
pop就是出栈，也就是返回。

其实这样就是逻辑上来说，比较清晰一点。做一些小操作。不用关心到底怎么来弄。

## 统一管理

在最外面定义。

```Dart
Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Code Sample for Navigator',
      // MaterialApp contains our top-level Navigator
      initialRoute: '/',
      routes: {
        '/': (BuildContext context) => HomePage(),
        '/signup': (BuildContext context) => SignUpPage(),
      },
    );
  }
```

方法里面调用

```Dart
 Navigator.of(context)
              .pushReplacementNamed('signup');
```

## 鸭子模式

定义一个类型，只有一个方法

```Dart
typedef void CartChangedCallback(Product product, bool inCart);

```

用 

```Dart
final CartChangedCallback onCartChanged;

```

## 可以参考的app

- [滑动](https://github.com/markgrancapal/filipino_cuisine)
    - 主要是滑动做的不错。这样可以用来做展现一个一个rumor

- [总例子](https://github.com/flutter/samples/blob/master/INDEX.md)
    - [有一些乱七八糟的例子](https://flutter.github.io/samples/#) 
- [很多layout的例子](https://medium.com/flutter-community/flutter-layout-cheat-sheet-5363348d037e)
     

## 资料

- [中文-weight](https://flutterchina.club/widgets/)
- [官网](https://flutter.dev/docs/development/ui/widgets-intro)
    - [widgets](https://flutter.dev/docs/development/ui/widgets) 
    - [例子的app](https://flutter.github.io/samples/#)
- [弹出窗口汇总](https://www.cnblogs.com/mengqd/p/12526884.html)
- [连续两点](https://blog.csdn.net/qq_26287435/article/details/90234530):就是返回自己。下面的methodA和methodB都是aa的方法。
```Dart
 aa..methodA()..methodB
```
 
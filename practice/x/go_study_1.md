---
description: 用Go些程序的时候，碰到知识点的总结
---

# Go的自学笔记-1

## 如何写interface
```Go

// at thing.go
type SimpleJob struct{ … }
func (t SimpleJob) execute() bool { … }

func NewJob() *SimpleJob { return SimpleJob{ … } }

// at main file

type Job interface{
    func execute() bool;
}

func main(){
    var job Job;
    job = NewJob()
}

```

从逻辑上来说，就是以下几点
- **在用的地方**定义接口
- 实现类**不在**语法层面**继承**接口，只是方法会不一样。
- 然后通过指针操作，使得使用的时候，通过指针和类型的转换来做。这个是Go的一个语法特性。

从语法层面来说，这里能够成立的原因，`var job Job`是一个**接口变量**，其本质式一个**指针**,而有些接口里面，可以用`v interface{}`来代表java中所有东西的作用。泛指所有东西

在定义的接口的时候，接口的方法要**大写**，不然实现的时候不忍。  
看了下编译时候的错误信息。说是没有实现的原因是接口的类和实现的类都带着**包名**，所以匹配不起来。
- 其实小写估计也能访问。只是无法直接访问。
- 然后指针检查是否实现，只是看有没有**指向的类**存不存在方法。


一切都是基于Go的语法特点，是“鸭子”类型。从java来说，这个是开辟了一个新的角度。java的类型继承。接口是在**实现侧**定义的。而Go因为鸭子类型的存在，使得这个可以定义在**使用侧**。        
就我个人经验来说，我觉得Go更加灵活和实际。在实现侧定义接口，如果事先能够了解需求，做充分的设计，才能做到十分优雅。否则的化，往往是一个接口定义于一个地方，不是优雅而是累赘。而Go的这种思路。往往不会产生这类累赘。要是觉得好用。如果真的有必要，确定之后归到实现侧其实也不迟。

## singleton

[关于singleton的介绍](http://marcio.io/2015/07/singleton-pattern-in-go/)：说的关注点基本和语言无关。单例模式，要考虑的是初始化的时候，然后就是性能问题， 只是go提供了一个方法，解决很多问题。

```Go
import (
    "sync"
)

type singleton struct {
}

var instance *singleton
var once sync.Once

func GetInstance() *singleton {
    once.Do(func() {
        instance = &singleton{}
    })
    return instance
}
```

这里面的once其实是一个变量。这个变量的`Do`只会被调用一次。

## map
[一个比较详细的介绍](https://www.jianshu.com/p/ba7852fbcc80)

- map**不是**线程安全的。具体解决办法在上面这篇文章中也写了。基本就就是继承。锁。然后加锁和释放锁，都在对那个对象
    - Go中的锁，居然还是分读锁，和写锁   
```Go
var counter = struct{
    sync.RWMutex
    m map[string]int
}{m: make(map[string]int)}
```
读
```Go
counter.RLock()
n := counter.m["some_key"]
counter.RUnlock()
fmt.Println("some_key:", n)
```
写
```Go
counter.Lock()
counter.m["some_key"]++
counter.Unlock()
```

- 获取值的时候，返回值里面包含是否存在`val,exists:= map[key]`
    - 这也就是Go的特色吧。总把一个结果的正常做为返回值返回
    - 这样，就不用lazy load map中的值了。反正都要取一次。
    
## 资料

- [官方关于写法的建议](https://github.com/golang/go/wiki/CodeReviewComments)
- [一篇第三方写的文章](https://www.integralist.co.uk/posts/go-interfaces/)
    - 写interface基本原则
    - 如何写测试
    - 写了一个例子，怎么来做优化。
- [effective go](https://golang.org/doc/effective_go.html)
    - [中文版](https://github.com/bingohuang/effective-go-zh-en) 

## 杂项
- [如何确定自己的版本](https://medium.com/@aman.sardana/go-modules-versioning-dependency-management-d5f96b490774)：从这篇文章里面，可以看到，主要还是通过git的tag。官方文档里面没有找到。
- [sync包](https://books.studygolang.com/The-Golang-Standard-Library-by-Example/chapter16/16.01.html),[说明二](https://deepzz.com/post/golang-sync-package-usage.html)
    - pool 各种对象的零时存放点
    - 各种锁
        -  互斥锁
        -  度写错
    - Cond 条件变量
    - Once 执行一次
    - WaitGroup  等待执行
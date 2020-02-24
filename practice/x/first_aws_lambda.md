---
description: test
---

# 第一个lambda程序

## makefile 教程

* [一个不错的makefile的教程](https://seisman.github.io/how-to-write-makefile/variables.html)  

## lambda相关

* 写lambda的时候，好像不能**太快**，直接返回值。否则貌似后台就接收不到返回值。
  * `time.Sleep(time.Duration(20)*time.Millisecond)`加了这句Go代码后，就发觉好了很多。这也是Go停止线程的方法
* 创建map，主要是用于文件头

  ```go
  var headers map[string]string = make(map[string]string)
    headers [ "Content-Type" ] = "application/json"
    headers [ "Access-Control-Allow-Origin" ] = "*"
  ```

* `ctx context.Context`: 就是显示一下文档里面的东西，没有环境变量
  * 必须放在handler里面的第一个。   

## DynamoDB

1. 在创建表的时候，所有的列都需要能够被index。否则不用出来，即使你需要。
   * 其实这个问题，我觉得还是一种理念的贯彻。如果**不用index**，那么就是**不用查**，那么也就是一个**附属**的属性。本身就没有必要出来。
2. ProvisionedThroughput： 性能相关的配置。越大，性能就越好。

## Log

[官方关于log的文档](https://docs.aws.amazon.com/lambda/latest/dg/golang-logging.html):基本都有。

* 写的话，比较随意。用`fmt`和`log`都可以
* 读的话，主要是用`aws cloudwatch`这个工具

个人使用下来，觉得主要还是级别太少。只有正常的print，fatal和panic。这个现阶段，我不是太清楚后两者的区别。不过如果用后两者来打log，**返回都会是错的**。这点就很不爽。不清楚怎么打warning级别的。

[一篇关于go 日志的文章](https://www.flysnow.org/2017/05/06/go-in-action-go-log.html): 讲的非常细节。基本也符合我的猜测，Go默认的logger，不会对logger级别想的太深。这点也许是go的简单的理念吧。

## 环境变量

[官方文档](https://docs.aws.amazon.com/lambda/latest/dg/golang-envvars.html)

* 获取用`os.Getenv(key)`: 这个方法获得。返回值为string。然后可以写到包里面
* 优先级是
  * `local start-api --env-vars envs/localdev.json` 这个变量
    * 需要指定属于函数还是怎么说
  * 函数的定义
  * 全局定义
* 所有的变量必须定义好，才能够被使用。

以下都是我自己试出来的 1. 所有变量都必须要在temalate里面做出定义 2. 如果不存在\(包括不在template\)文件里面。默认值都是**空字符串**

### Global Variable

要获得这个效果，有两条路

* 系统级别的Parameter。然后在Function级别，用`!Ref SomeVar`来引用。这个表示不设值的时候使用
* `Globals-Function-Enviroment`:一路下来。

### --env-vars 覆盖

```javascript
{
    "Parameters": {
        "BAR": "helloworld"
    }
}
```

* [具体说明](https://github.com/awslabs/aws-sam-cli/issues/1045)：就是在那里加了`Paramters`这个选项。

然后我发现一个bug。就是会disable Function级别的

```javascript
{
  "HelloWorldFunction": {
    "SOME_VAR": "123",
    "NOT_EXISTS_IN_TEMPLATE": "true"
  },
  "Parameters": {
    "GLOBAL_VAR": "local"
  }
}
```

上面的情况下。`HelloWorldFunction`是不工作的。

## resource列表

* [CloudFormation resource全集](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
  * [CloudFormation examples](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/CHAP_TemplateQuickRef.html) 
* [serverless-application-model](https://github.com/awslabs/serverless-application-model)
  * [2016的resource列表](https://github.com/awslabs/serverless-application-model/blob/master/versions/2016-10-31.md)

## 坑

1. 因为在定义结构的时候，属性是小写，所以造成了json无法反序列化
2. 我自己搭了一个dynamodb的[dynamodb-gui](https://github.com/Arattian/DynamoDb-GUI-Client.git)这个第三方客户端。发觉数字被四舍五入了，然后发现是一个显示问题。顺便找了一个最简单的扫描全表的代码。如下：
   * [关于dynamodb data type的官方文档](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html)

     ```javascript
     var params = {
     TableName: 'rumor',
     Select: 'ALL_ATTRIBUTES'
     };
     dynamodb.scan(params, function(err, data) {
     if (err) ppJson(err); // an error occurred
     else ppJson(data); // successful response
     });
     ```
3. Go的随机，不会每次帮你生成新的seed，需要你自己手动生成。最简单的就是用时间

   ```go
   rand.Seed(time.Now().UnixNano())
   rand.Int()
   ```


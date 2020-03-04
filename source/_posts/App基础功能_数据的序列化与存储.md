---
title: iOS数据的序列化与存储
date: 2020-03-4 0:55:26
categories:
- [iOS, 基础, 存储]
tags:
- 编程
---

## JSON解析 

### NSJSONSerialization

(系统最底层)

* 系统最基础的库： NSJSONSerialization 提供JSON数据和系统对象之间的转换（可以相互转换）

```objc
// 把收到NSData转成Dictionary的对象
NSError *jsonError;
// change to NSdata to Dictionary
id jsonObj = [NSJSONSerialization JSONObjectWithData:data options:0 error:&jsonError];  
```



### 开源的解析库

* 用一个Model来代表Dictionary汇总的数据（Bean）

  JSON Model开源项目： **YYModel** / Mantle / MJExtension

* 简化NSData - JSON - Model流程
* 避免类型转换错误 / 属性和对象不一致
* 相互转换

**建议使用开源的项目直接把Dictionary变成一个Model，但是在objectForKey的时候也是需要异常检测的，防止拿不到数据。**

```objc
JSON:
{
   "uid":123456
   "name":"Victor"
   "create":"1965-07-31T00:00:00+0000"
}

// Model:
@interface User : NSObject
@property UInt64 uid;
@property NSString *name;
@property NSDate *created;
@end
@implementation User
@end

// Convert json to model:
User *user = [User yy_modelWithJSON:json];  // json is NSDictionary

// Convert model to json:
NSDictionary *json = [user yy_modelToJSONObject];
```



## 完整列表加载流程

* 在合适的时机通过网络接口加载数据
* 使用NSJsonSerialization解析处理网络请求
* 数据Model(Bean)化
* 列表加载Model数组

**通过Loader请求数据的时候通过一个Block来传回数据，并且会涉及到主进程问题**



## iOS文件

* 每个App是一个沙盒，只能访问自己沙盒之中的文件了

  ![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15725910914753.jpg)

* 沙盒机制App只能访问自己的目录

* Documents： 可以进行备份和恢复，体积较大，一般存档用户数据（如音乐App 音乐存在这里） 语义上用户生产和共享的文件

* Library：最常用，可以自定义子文件夹，会被同步到icloud中

  ![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15725913707522.jpg)

* SystemData：系统提供的数据

* tmp: 临时文件夹，启动时候可能会被清空

* Documents和Library最大的区别可以共享文件，设置plist就可以共享。默认是不支持共享的。
* Library为最常用文件夹，可以用户自定义文件夹，如在网络的时候可以吧网络数据都存在这里，命名通常是这个网络资源的倒叙。 （系统默认有Cache文件夹和Preference，可以再对应Cache文件夹中继续在建立文件夹存一些用户的缓存）



### NSPathUtilites

获取沙盒地址

`NSArray *pathArray = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);`  取出文件地址 返回的是一个数组，第一个数为地址
* 第一个参数是哪个文件夹(Document或Library或其子文件夹等等)
* 第二个参数是指取用户的文件夹还是系统的文件夹

以下拿到沙盒地址
```objc
NSArray *pathArray = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES);
NSString *cachePath = [pathArray firstObject];
```



### NSFileManager

通过NSFileManager来对文件夹及文件操作

* **fileManager是一个单例**。 主要用于创建文件、删除文件、查询文件、移动和复制等等。根据用户给出的路径操作
* 创建文件，删除文件，查询文件，移动和复制文件等
* 读取文件内容
* 通过NSURL或者NSString作为Path进行操作


`NSFileManagerDelegate` 提供移动、复制、删除等操作的具体定义实现

```objc
// fileManager是一个单例。 用于创建文件、删除文件、查询文件、移动和复制等等。根据用户给出的路径操作
NSFileManager *fileManager = [NSFileManager defaultManager];
    
// 创建文件夹
NSString *dataPath = [cachePath stringByAppendingPathComponent:@"YXData"];  // 在cachePath地址的后面追加这个地址
NSError *createError;
[fileManager createDirectoryAtPath:dataPath withIntermediateDirectories:YES attributes:nil error:&createError];  // 创建一个文件夹根据dataPath

// 创建文件
NSString *listDataPath = [dataPath stringByAppendingPathComponent:@"list"];
NSData *listData = [@"abc" dataUsingEncoding:NSUTF8StringEncoding];      // 使用utf8编码字符串，变成一个二进制文件。计算机是以二进制存储的
[fileManager createFileAtPath:listDataPath contents:listData attributes:nil];      // 在给定路径创建文件，内容contents为listData(字符abc)

// 查询文件
BOOL fileExist = [fileManager fileExistsAtPath:listDataPath];  // 判断listDataPath是否z存在

// 删除
if (fileExist) {
    [fileManager removeItemAtPath:listDataPath error:nil];
}
```



### NSFileHandle 

对指定文件读写


* **NSFileHandle是单例** 。 读/写文件
* 常用于追加操作 （在offset移动到文件莫问，然后追下字符）
* 记得操作完关闭文件

为文件追加字符串在最后
```objc
// 单例。 读/写/截断/刷新/追加数据等等。常用于追加数据。 以下对listDataPath的这个文件创造了一个NSFileHandle对其进行操作
NSFileHandle *fileHandler = [NSFileHandle fileHandleForUpdatingAtPath:listDataPath];

// 对文件数据追加操作
[fileHandler seekToEndOfFile];  // 把offset调到文件末尾，因为要在文件末尾追加数据
[fileHandler writeData:[@"--append: def" dataUsingEncoding:NSUTF8StringEncoding]];  // 使用utf8编码字符串写在offset后面

[fileHandler synchronizeFile];  // 刷新文件 对实时要求高就刷新
[fileHandler closeFile];  // 关闭文件操作。不关系统结束才关，但是最好每次使用完就关
```



## 序列化和反序列化对象

### NSCoder
* 基类，用户不会直接使用这个类。系统在之上对其封装了
* 提供简单的函数，在Object和二进制数据间进行转换
* 在序列化和反序列化的，为了向前向后兼容，不依赖顺序，所以需要将非基本数据对应到key存储(PBCoding)。 就是在序列化的时候需要自定义一个Key做为键，一般方便使用属性的名称做为Key或者自定义Key



### NSKeyedArchiver & NSKeyedUnarchiver

* NSCoder的子类。 使用比较多
* 提供简单的函数，在Object和二进制数据间进行转换 (Object序列化成二进制流， 二进制流反序列化成Object)
* 需要Object自己处理Key - Value对应关系
* 提供基本的Delegate给用户进行使用 
* 序列化(NSKeyedArchiver中的方法)：`archivedDataWithRootObject:requiringSecureCoding:error:`
* 反序列化(NSKeyedUnarchiver中的方法)：`unarchivedObjectOfClasses:fromData:error:`



### NSCoding协议 & NSSecureCoding协议(安全)

* 需要序列化一个对象，给对象需要实现该协议
* NSArray & NSDictionary等类型系统已经实现该协议
* NSSecureCoding在最新系统需要使用，更加安全。（解决文件替换攻击，序列化时规定Class）



### NSUserDefault

* 提供简单的key - value存储
* **单例** `[NSUserDefaults standardUserDefaults]`，存取轻量级的数据(一般用于用户的偏好设置) 
* 升级安装后还可以继续使用(因为保存在本地文件夹中，所以App升级后也是可以看到的)
* 文件存储在 /Library/Preferences下
* 支持基本数据类型，但是复杂的Model需要转成NSData之后存储。 Integer Float Double Bool NSArray NSData NSString NSDictionary等
* 存方法`setObject:forKey:`. 取方法`dataForKey:`

```objc
[[NSUserDefaults standardUserDefaults] setObject:listData forKey:@"listData"];  // 通过key存 (listData是一个二进制流)
    NSData *test = [[NSUserDefaults standardUserDefaults] dataForKey:@"listData"];  // 通过key取
```
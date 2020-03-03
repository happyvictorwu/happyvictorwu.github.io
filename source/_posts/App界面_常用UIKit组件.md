---
title: App界面 - 常用UIKit组件
date: 2020-03-03 00:07:48
categories:
- [iOS, 基础, UIKit]
tags:
- 编程
---

# App界面 - 常用UIKit组件


## 设计模式

### MVC（开发者使用数据Model驱动）
* 界面View （展示） --- UIView
* 数据为 --- Model
* Controller管理数据(Model)如何在视图（View）上展示 --- UIViewController



### Delegate(相互)

* 对类的设计者：在类的流程中暴露出一些方法，可以放用户自定义在这个时候的操作。自定义在这个逻辑。暴露一个delegate的对象给用户，让使用者使用delegate对象实现操作。
* 设置self为delegate的接受者，然后根据需求实现delegate的方法。required必须实现。

|                            设计者                            |                  使用者                  |
| :----------------------------------------------------------: | :--------------------------------------: |
| 提供一些使用者可以自定义的操作<br />`@optional/@required`注解<br />提供 `@property - delegate`<br />在对应的时机，让delegate执行对应的方法 | 设置 `delegate = self`<br />按需实现方法 |



### 观察者模式

* 定义一种多对多的关系，可以让多个观察者同时监听某一个对象或者对象的属性的变化。

* 在被监听的对象在状态变化时候，会通知所有的观察者。

* 跟代理delegate模式的区别： Delegate只能给一个VC（说白了是1对1， 然后在1对多），VC存在一个该对象。

* 广播， KVO都是观察者模式的实现

  * KVO

    ```objc
    /*  注册监听：
    self 作为监听者，接受事件
    监听self.webview的estimatedProgress属性
    在NSKeyValueObservingOptionNew的时候发通知 
    */
    [self.webview addObserver:self
    			   forKeyPath:@"estimatedProgress"
    				   option:NSKeyValueObservingOptionNew
    				  context:nil];
    
    // 移除监听
    [self.webview removeObserver:self forKeyPath:@"estimatedProgress"];
    
    /* 接受通知
    change 对应着上面的option
    */
    -（void)observeValueForKeyPath:(nullable NSString *)keyPath
     	ofObject:(nullable id)object
          change:(nullable NSDictionary<NSKeyValueChangeKey, id> *)change
         context:(nullable void *)context {
    	// 业务逻辑
    }
    ```

    * 注册监听：`self.webview` 添加一个观察这self去监听自己的"estimatedProgress"属性的变化。 `option`是变化什么时候发送通知
    * 接收通知：观察者`self`需要实现对应接收通知的的方法。
    * 移除通知：在观察这被销毁的时候需要移除监听！！！



### Target-Action（交互）

* 限制传值不灵活
* 让一个**对象**监听一个事件**Events** 让target执行action

```objc
[self.button addTarget:self
			    action:@selector(clickButton:)
	  forControlEvents:UIControlEventTouchUpInside];
// UIControlEventTouchUpInside是button组件内部监听事件
```





## UIView

* 最基础的类，管理渲染屏幕区域的内容展示

* 作为各种View类型的父类，有基础属性(frame)和能力（addSubView etc.）

* 属性**bounds表示相对于自己坐标系的位置和大小(rect)，frame表示相对于父坐标系的位置和大小**

* 使用栈管理全部子View，栈顶的先显示。addSubView就是压一个View到SubViews Stack中

* **生命周期** 一个View作为SubView添加到另一个View的时候会经历创建周期方法：

  `init`方法 

  → `willMoveToSuperview:`方法 

  → `didMoveToSuperview`方法 

  → `willMoveToWindow:`方法 

  → `didMoveToWindow`方法



## UIViewController

* 创建的时候会默认给一个UIViewController Default View(给一个自身默认的view)

* 是一个包含多个View的容器

* 管理每一个View视图的声名周期，相应用户操作

* 作为一个整体切换，即Controller中所有的View都会是一个整体被切换

* 也可以管理多个Controller切换和动画

* **生命周期** 流程：

   `init`(创建Controller) 

  → `viewDidLoad`(里面所有的View在内存加载完毕，还没显示，所以很多初试化在此操作) 

  → `viewWillAppear:`(开始加载到屏幕的瞬间，每次显现到该VC时) 

  → `viewDidAppear:`（加载完成的瞬间） 

  → `viewWillDisappear:`（即将离开屏幕的瞬间） 

  → `viewDidDisappear:`（完全离开屏幕的瞬间） 

  → `Dealloc`（销毁的时候）



### UITabBarController

* 组成ViewControlers（对应很多个VC），还有一个TabBar（控制选择哪一个VC）
* 继承自UIViewController
* 默认是没有视图的（Default View）要自己设置
* 管理多个UIViewController切换。通过底部对应的UITabBar按钮，选中对应的UIViewController。
* 实例方法： `setViewControllers:@[c1, c2, c3, c4];`可以添加不同的Controller（顺序为跟显示一样）。对每一个c，以c1为例设置对应的属性`c1.tarBarItem.title` `c1.tarBarItem.image` `c1.tarBarItem.selectedImage`



### UINavigationController

![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15652452700065.jpg)

* 继承 UIViewController
* 可以Push和Pop ViewController
* 只显示栈顶元素 （跟UIView加进UIViewController中一样）
* UINavigationBar `title` `backBarButtonItem` `backIndicatorImage` `leftBarButtonItem`在包含在NavigationBar里面的Controller的属性自己设置，跟UITabBarController一样在设置，属性在保管的Controller自己里。
* 默认没有视图，要自己设置rootViewController**（必须）**。跟UITabBarController一样 `UINavigationController *navigationController = [UINavigationController alloc] initWithRootViewController:(根Controller);`
* 在其中的根Controller或者其他Controller中push另一个界面push一个新界面进来，实例方法名为**pushViewController**（navigationController在AppDelegate中）`[self.navigationController pushViewController:（新界面Controller） animated:YES];`



## UIWindow

* 窗口，最基础的容器，是一个特殊形式的UIView。  最底层的View

* **一般**只有一个UIWindow最上层，主窗口。

* 结合ViewController一起协同工作.

* 使用storyborad会自动创建一个UIWindow（若要删除则需要）

* 删除默认storyboard方法：1.右键删除 2.在plist中把Main storyboard file base name设置也删除.

* 手动创建：三步：`创建UIWindow` `设置rootViewController` `makeKeyAndVisible` 

* 手动创建具体：①在AppDelegate（iOS13后为SceneDelegate）中 `application:didFinishLauchingWithOptions:`创建。 ②添加如下三步代码`self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen] bounds]; // 创建UIWindow并把它变成屏幕大小` `self.window.rootViewController = (想要设置的根viewController)` `[self.window makeKeyAndVisible]; // 展示UIWindow`

* UIWindow两种常用的UI框架： 

  1. 苹果官方推荐：UIWindow的rootViewController是**TabBarController**，然后TabBarController中依次放NavigationController。（动画效果是收敛在**TabBarController**当中的） 

     ![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15653206448193.jpg)

  2. UIWindow的rootViewController放的是**NavigationController**，然后NavigationController的root是一个TabBarController。（整个TabBarController作为一个整体被推推走了）。

     ![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15653206655651.jpg)

> 
>
> iOS13之后，Appdelegate的指责发生了变化，具体的可以参考https://developer.apple.com/videos/play/wwdc2019/42 这个session。 简单的说和UI生命周期相关的回调都交给了SceneDelegate 这个delegate，而appdelegate主要负责 app 生命周期相关的回调。对于window来说，作为一个特殊的UI，也自然的变成了SceneDelegate的一个property，我们可以在SceneDelegate的头文件中找到它，并在willConnectToSession这个函数中使用。同时在删除main.storyboard的时候，在和课程中相同的删除操作之上，也需要删除在info.plist中的Application Scene Manifest中对应storyboard的默认设置



## UIScrollView

![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15653382781813.jpg)

* 回调的Delegate: 实时获取监控`scrollViewDidScroll:`开始拖拽`scrollViewWillBeginDragging:`完成拖拽、减速加速等等

* 应用场景
  ![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15653399091199.jpg)
  * 竖向是UITableView或UICollectionView，横向是UIScrollView
  * **功能上**继承关系UIView - UIScrollView - UITableView(UICollectionView)
  * **Delegate**上 UIScrollViewDelegate - UICollectionViewDelegate(UITableViewDelegate) 所以在实现了后面两个Delegate的时候也可以实现UIScrollViewDelegate的方法（子类Delegate）



### UITableView

![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15653214745508.jpg)
* 一个储存格为一个Cell 竖着排列。

* 取屏幕尺寸：`UIScreen.mainScreen().bound.size` 

* 创建流程： 

  1. 建立一个UITableView并设好约束。 
  2. 注册Cell（可以用TableView自带的或自己创建） 
  3. 设置Delegate `tableView.delegate = self;` `.dataSource=self` 
  4. 分割线样式属性.separatorstyle. 间距:UIEdgeInsetsMake(上,下,左,右) 是否能点（allowSelection, allowsMultipleSelection） 
  5. `self.view addSubview:`

* addSubview可以通过一个闭包的写法。
![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15655795563667.jpg)

* UITableViewDelegate的代理required方法

  `heightForRowAtIndexPath: // 返回TableView每一行的高度`

  `didSelectRowAtIndexPath: // 点每一行的时候触发逻辑，根据indexPath来确定点了什么位置` 

* UITableViewDataSource的require方法：

  `numberOfRowInSection //每一组有几个cell`

   `cellForRowAtIndexPath // 每个cell是什么内容，基于IndexPath`

* IndexPath: section和row(把section理解成章， row是节) 默认是一个section。

  ![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15653350487203.jpg)

* 重用和复用回收池：思路是在cellForRowAtIndexPath方法中拿cell的时候，先在系统的回收池看看能不能拿到这个cell，能拿到就可以复用该cell，不能就创建一个。

  ```objc
  UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"你的Cell ID"];
  if (!cell) {
      cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleSubtitle
                                    reuseIdentifier:@"Cell ID"];
  }
  ```

* Cell可以通过。Xib文件创建。也可以通过代码重写注册方法创建。在self.contextView addSubView就可以设置了。



### UICollectionView
![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15653354495747.jpg)
*  弹幕功能的实现

* UICollectionViewDelegate 常用代理方法

  `willDisplayCell / endDisplayCell` 

  `collectionView:didSelectItemAtIndexPath: // 选中某一个执行` 

* UICollectionDataSourceDelegate常用代理方法

   `numberOfItemsInSection: // 有多少个Item` `cellForItemAtIndexPath: // cell长什么样子` 

* indexPath: section(第几行) **item**(一行的第几个，这里跟TableViewController不同)要区分第几个用row不合适，但就是单词换了，逻辑是一样的）

* 跟TableView不同的是，**必须先注册Cell类型用于重用**步骤：

  1. `registerClass:forCellWithReuseIdentifier: // 注册` (在一开始注册就行，不用再每次都调用) 
  2.  `dequeueReusabelCellWithReuseIdentifier:forIndexPath: // 重用` 
  3. 要设置一个flowLayout（流式布局）。 其他都类似TableView

  ```objc
  // 在代理方法中
  - (__kindof UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath {
      UICollectionViewCell *cell = [collectionView 		  
                                    dequeueReusableCellWithReuseIdentifier:@"id" 
                                                              forIndexPath:indexPath];
      return cell;
  }
  ```

* 创建UICollectionView的时候要用collectionViewLayout(一般flowLayout)。



#### UICollectionViewFlowLayout

*  流式布局，每行排满后自动换行

* 流式布局属性

  `.minimumLineSpacing = 10`（对应两个section之间的距离） 

   `.minimumInteritemSpacing = 10`（对应一个section中每个item间的距离）

   `.itemSize = CGSizeMake((self.view.frame.size.width - 10) / 2, 300);` （item的大小）



## UILabel 

(展示一行或多行只读文字视图)
* text: `@"123"`

* font: UIFont(大小、粗体、斜体)

* textColor:文字颜色

* textAlignment: 对齐方式（居中、左对齐、右对齐）

* numberOfLines:最大展示行数

* lineBreakMode：长度不够时截断方式（截断左边，截断中间）,以`victor's iPhone`为例

  `NSLineBreakByClipping` : `victor's iPh`（切掉右边，不带省略号）

  `NSLineBreakTruncatingTail`:`victor's iPh...`（省略右边）

  `NSLineBreakTruncatingMiddle`：`vict ... iPhone` （省略中间）

  `NSLineBreakTruncatingHead` :`...tor's iPhone` （省略头部）

* 函数`(void)sizeToFit`：可变大小



## UIImage -> UImageView

* 通过UIImageView来展示： 常用图片类型：png、jpeg、pdf

* 获取UIImage对象系统方法总结：

  `+ (nullable UIImage *)imageNamed:(NSString *)name;`

   `+ (nullable UIImage *)imageWithContentsOfFile:(NSString *)path;` 

  `+ (nullable UIImage *)imageWithData:(NSData *)data;`

   `+ (UIImage *)imageWithCGImage:(CGImageRef)cgImage;`

* **UIViewContentMode**:当图片的尺寸和UIImageView尺寸不符合发的时候，自定义填充。 

  ```objc
  typedef NS_ENUM(NSInteger, UIViewContentMode) {
  //图片拉伸填充至整个UIImageView(图片可能会变形),这也是默认的属性,如果什么都不设置就是它在起作用
      UIViewContentModeScaleToFill,
  //图片拉伸至完全显示在UIImageView里面为止(图片不会变形)
      UIViewContentModeScaleAspectFit,
  //图片拉伸至图片的的宽度或者高度等于UIImageView的宽度或者高度为止.看图片的宽高哪一边最接近UIImageView的宽高,一个属性相等后另一个就停止拉伸.
      UIViewContentModeScaleAspectFill,
  //调用setNeedsDisplay 方法时,就会重新渲染图片
  
  //下面的属性都是不会拉伸图片的
      UIViewContentModeRedraw,
  //中间模式
      UIViewContentModeCenter, 
  //顶部
      UIViewContentModeTop,
  //底部
      UIViewContentModeBottom,
  //左边
      UIViewContentModeLeft,
  //右边
      UIViewContentModeRight,
  //左上
      UIViewContentModeTopLeft,
  //右上
      UIViewContentModeTopRight,
  //左下
      UIViewContentModeBottomLeft,
  //右下
      UIViewContentModeBottomRight,
  };
  ```

  


## UIButton
 对UIControl(继承自UIView)（**不是UIController**）进行封装，实现了UIControl自定义的功能。可以展示（根据不同状态展示不同） 和 交互。

 * 常见状态

     `UIControlStateNormal(正常状态)`

     `UIControlStateHighlighted(选中状态)`

    e.g. `[self.button setTitle:@"..." forState:UIControlStateNormal]`

* 监听对应事件，通过Target-Action处理一些点击事件

* UIButton通过Target-Action的模式，处理点击逻辑

    ```objc
    [self.button addTarget:self
    ```

    



## UIGestureRecognizer

* 可以在任何视图增加一个或者多个手势。系统自动识别手势。开发者自定义响应逻辑。

* 采用Target - Action的方式进行处理

* 流程：创建手势 - 设置响应处理 - 在视图中添加

  ```objc
  // 注册手势
  UITapGestureRecognizer *tapGesture = 
      [[UITapGestureRecognizer alloc] initWithTarget:self 
                                      action:@selector(viewClick)]
  
  // 在view中添加手势
  [view addGestureRecognizer:tapGesture];
  ```

* 可以通过UIGestureRecognizerDelegate来实现对应状态。

* 常见手势封装：`Tap`、`Pitch`、`Rotation`、`Swipe`、`Pan`、`LongPress`



## ~~UIAlertView（已经废弃了）~~

UIAlertView is **deprecated**. iOS8之后UIAlertView被弃用 UIAlertController成功上位。
![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15655770408648.jpg)
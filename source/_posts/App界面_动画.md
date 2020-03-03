---
title: App界面 - 动画
date: 2020-03-03 19:14:23
categories:
- [iOS, 基础, 动画]
tags:
- 编程
---

# App界面 - 动画
CoreAnimation - (UIView动画) （UIKit组件自带动画）

* UIView动画： 渐隐渐显 位置移动
* UIKit组件自带动画 ：1. UITableViewCell 添加删除 2. UIViewController 切换



## UIView动画
只要继承自UIView都可以有的动画
* 解决日常开发80%以上的动画效果，处理基本的Frame、 Alpha、 Transform，

  但不能自定义中间过程，相当于原子操作动画。

  **实现步骤：1. 设置动画参数（时间、效果）2.动画终止时属性的最终值**

  ```objc
  + (void)animateWithDuration:(NSTimeInterval)duration /* 动画时间 */
      				  delay:(NSTimeInterval)delay /* 动画延迟 */
       usingSpringWithDamping:(CGFloat)dampingRatio /* 阻尼系数 */
        initialSpringVelocity:(CGFloat)velocity /* 回弹系数 */
                  	options:(UIViewAnimationOptions)options /* 效果 */
                 animations:^{
                          // 添加动画最终样式
                 } completion:^(BOOL finished) {
                          // 动画结束之后逻辑
                 }
  ```

  * 最常用的options的选择动画效果(摘自`UIViewAnimationOptions`)

    ```objc
    UIViewAnimationOptionCurveEaseInOut = 0 << 16, // default
    UIViewAnimationOptionCurveEaseIn = 1 << 16,
    UIViewAnimationOptionCurveEaseOut = 2 << 16,
    UIViewAnimationOptionCurveLinear = 3 << 16,
    ```

* 给定UIView的一个终止位置，跟根据起始位置进行动画的自动展示，示例代码

  ```objc
  /* 
  iOS13废弃了.keyWindow属性 解决办法：https://stackoverflow.com/questions/57134259/how-to-resolve-keywindow-was-deprecated-in-ios-13-0
  
  需要先把keyWindows添加自身的View， 在销毁的时候再移除 [self removeFromSuperview]
  */
  // [[UIApplication sharedApplication].keyWindow addSubview:self];  
  
  [UIView animateWithDuration:1.f delay:0.f usingSpringWithDamping:0.5 initialSpringVelocity:0.5 options:UIViewAnimationOptionCurveEaseInOut animations:^{
          self.deleteButton.frame = CGRectMake((self.bounds.size.width - 200) / 2, (self.bounds.size.height - 200) / 2, 200, 200);
      } completion:^(BOOL finished) {
          NSLog(@"");  // finish animation and do something
      }];
  ```

  

### 步骤操作说明

 (这也是Delegate的通用实现步骤)

顺便复习一下Delegate模式，自定义实现一个代理方法
*PS：UITableView简称TV, UITableViewCell简称TVCell， UIButton简称Bt*

需要完成任务：在TV点击TVCell中的删除Bt， TV获取对应TVCell的对象以及VTCell中的属性BT

#### 在TVCell声明Delegate
1. 在TVCell的.h文件中声名一个Delegate。并向外暴露一个属性名字为delegate。 并定义好函数，函数为点击删除按钮的操作`tableViewCell:clickDeleteButton:`

     ```objc
     // YXNormalTableViewCell.h
     
     // 自己实现是一个Delegate，当点击删除按钮的时候去触发这个Delegate
     @protocol YXNormalTableViewCellDelegate <NSObject>
     
     // 告诉Delegate给的是哪个cell和哪个button
     - (void)tableViewCell:(UITableViewCell *)tableViewCell clickDeleteButton:(UIButton *)deleteButton;  
     
     @end
     
     @interface YXNormalTableViewCell : UITableViewCell
     
     // 这里是weak类型！！！
     @property (nonatomic, weak, readwrite) id<YXNormalTableViewCellDelegate> delegate;  
     
     // ...
     
     @end
     ```
2. 在TVCell的.m文件中设置删除Bt(deleteButton)的点击事件Target-Action @selector(deleteButtonClick)，去触发这个Delegate，并把当前的TVCell和Bt传给实现者

     ```objc
     // YXNormalTableViewCell.m
     
     - (void)deleteButtonClick {
         // 因为这里这里的Delegate是可选的，所以要看看delegate是否实现了对应的方法
         if (self.delegate && [self.delegate respondsToSelector:@selector(tableViewCell:clickDeleteButton:)]) {
             [self.delegate tableViewCell:self clickDeleteButton:self.deleteButton];  // pass TVCell and Bt as parameters to the delegate object 
         }
     }
     ```

     

#### 在TV中的实现Delegate

1. 在.m文件的开头声名要实现的这个Delegate。并生成每一个TVCell的时候，都把每一个Cell的delegate属性设置成self，即当前处理的ViewController
    ```objc
    // ViewController.m

    // 声名这个 YXNormalTableViewCellDelegate
    @interface ViewController ()<YXNormalTableViewCellDelegate, UITableViewDataSource, UITableViewDelegate> 

    // ...

    @end

    // ...

    - (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
        YXNormalTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"id"];  // 使用复用回收机制，在tableview的回收池中能不能找到这个相同id的cell（相同id的样式是一样的）

        if (!cell) {  // 若没有cell,创建一个新的cell。
            cell = [[YXNormalTableViewCell alloc] initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier:@"id"];
            cell.delegate = self;  // !!!! 把Delegate给当前处理的ViewController,即self !!!!
        }

        [cell layoutTableViewCell];

        return cell;
    }
    ```
2. 实现这个代理方法，获取到该点点击的TVCell和Bt的信息，执行删除逻辑
    ```objc
    // MARK: - 实现自定义的YXNormalTableViewCellDelegate
    - (void)tableViewCell:(UITableViewCell *)tableViewCell clickDeleteButton:(UIButton *)deleteButton {
        NSLog(@"代理方法 - 点击了删除按钮,并获得对应的tableViewCell和deleteButton");

        // ... 删除逻辑

    }
    ```
    [有点像这篇文章的传值操作，还有更多用法，参考文章](ttps://www.jianshu.com/p/b351a759cdcc)



### Block来回调点击Cell之后更新UITableView的操作

通过函数，传入一个Block可以为声名这个Block的地方保存住这个代码块（截断），之后可以在内部执行这个代码块。 但是要注意循环引用。 （简单理解就是 传值 函数调用，但不准确）

* 删除TableView的细节，因为在TableView的一个Delegate方法`tableView:numberOfRowsInSection:`中要返回TableView的个数，这样在删除的时候使用方法`deleteRowsAtIndexPaths:indexPathForCell:withRowAnimation`删除后需要将Delegate的个数减一，所以用一个数组来记录元素的个数，而元素是什么不重要，只要是长度。



## CALayer

* 每一个UIView的CALayer （一个delegate）负责内容的绘制和动画， 可以理解成是属性
* 使用UIView的CALayer实现圆角 `@property(nonatomic,readonly,strong) CALayer *layer; `

```objc
// setting layer
self.deleteButton.layer.cornerRadius = 10;
self.deleteButton.layer.masksToBounds = YES;
    
self.deleteButton.layer.borderColor = [UIColor lightGrayColor].CGColor;
self.deleteButton.layer.borderWidth = 2;
```



## CoreAnimation (CALayer的CA的全称)

* 关系：layer的底层， 系统提供给用户自定义动画的方法，做完加到CALayer上显示动画。 （可以自定义中间过渡动画）

* 关系： UIView有一个layer， 系统默认实现一套

![](https://raw.githubusercontent.com/happy-yuxuan/picgo/master/img/15722316014136.jpg)



动画实现：（开源项目）

* Lottie （通过JSON实现动画，从后台发JSON，在客户端播放JSON就可以播放动画了）
* facebook / pop
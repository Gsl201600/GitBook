---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# iOS 获取屏幕最上层window以及响应者

**1. 通过UIApplication获取**

```
UIWindow *window = [UIApplication sharedApplication].keyWindow;
或者
UIWindow *window = [[UIApplication sharedApplication].windows lastObject];
```

**2. 比较严谨的获取方法：**

```
- (UIWindow *)lastWindow{
    NSArray *windows = [UIApplication sharedApplication].windows;
    for (UIWindow *window in [windows reverseObjectEnumerator]) {
        if ([window isKindOfClass:[UIWindow class]] && CGRectEqualToRect(window.bounds, [UIScreen mainScreen].bounds)) {
            return window;
        }
    }
    return [UIApplication sharedApplication].keyWindow;
}
```

**3. 查找响应者**

```
// 新建一个UIView的类目，把这个方法放进去，以后就可以直接通过view.findResponderViewController来获取视图的控制器了。
- (UIViewController *)findResponderViewController{
    UIResponder *next = self.nextResponder;
    do {
        if ([next isKindOfClass:[UIViewController class]]) {
            return (UIViewController *)next;
        }
        next = next.nextResponder;
    } while (next != nil);
    return nil;
}
```

[附：我的博客地址](https://gsl201600.github.io/2019/02/27/iOS%E8%8E%B7%E5%8F%96%E5%B1%8F%E5%B9%95%E6%9C%80%E4%B8%8A%E5%B1%82window%E4%BB%A5%E5%8F%8A%E5%93%8D%E5%BA%94%E8%80%85/)

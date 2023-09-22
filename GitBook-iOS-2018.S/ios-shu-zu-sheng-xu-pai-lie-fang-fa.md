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

# iOS 数组升序排列方法

```
NSArray *orignalArr = @[@"3", @"12", @"4", @"1", @"4", @"8"];
NSArray *resultArr = [orignalArr sortedArrayUsingComparator:^NSComparisonResult(id  _Nonnull obj1, id  _Nonnull obj2) {
    if ([obj1 integerValue] > [obj2 integerValue]) {
        return NSOrderedDescending;
    }else if ([obj1 integerValue] < [obj2 integerValue]){
        return NSOrderedAscending;
    }else{
        return NSOrderedSame;
    }
}];
NSLog(@"升序排列的数组:%@", resultArr);
```

[附：我的博客地址](https://gsl201600.github.io/2019/02/22/iOS%E6%95%B0%E7%BB%84%E5%8D%87%E5%BA%8F%E6%8E%92%E5%88%97%E6%96%B9%E6%B3%95/)

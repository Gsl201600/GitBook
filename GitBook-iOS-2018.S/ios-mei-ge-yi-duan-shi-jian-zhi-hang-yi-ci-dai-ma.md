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

# iOS 每隔一段时间执行一次代码

```
//每隔一分钟执行一次打印
// GCD定时器
static dispatch_source_t _timer;
//设置时间间隔
NSTimeInterval period = 60.f;
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
_timer = dispatch_source_create(DISPATCH_SOURCE_TYPE_TIMER, 0, 0, queue);

// 第一次不会立刻执行，会等到间隔时间后再执行
dispatch_time_t start = dispatch_time(DISPATCH_TIME_NOW, period * NSEC_PER_SEC);
dispatch_source_set_timer(_timer, start, period * NSEC_PER_SEC, 0);

// 第一次会立刻执行，然后再间隔执行
dispatch_source_set_timer(_timer, dispatch_walltime(NULL, 0), period * NSEC_PER_SEC, 0);
// 事件回调
dispatch_source_set_event_handler(_timer, ^{
    dispatch_async(dispatch_get_main_queue(), ^{
        NSLog(@"Count");
        //网络请求 doSomeThing...
    });
});
    
// 开启定时器
if (_timer) {
    dispatch_resume(_timer);
}
    
// 关闭定时器
if (_timer) {
    dispatch_source_cancel(_timer);
    _timer = nil;
}
```

* 第一次不会立刻执行，等到间隔时间后再循环执行

```
dispatch_time_t start = dispatch_time(DISPATCH_TIME_NOW, period * NSEC_PER_SEC);
dispatch_source_set_timer(_timer, start, period * NSEC_PER_SEC, 0);
```

* 第一次会立刻执行，然后再间隔执行

```
dispatch_source_set_timer(_timer, dispatch_walltime(NULL, 0), period * NSEC_PER_SEC, 0);
```

* 延迟10s执行一次

```
dispatch_time_t time = dispatch_time(DISPATCH_TIME_NOW, 10.0 * NSEC_PER_SEC);
dispatch_after(time, dispatch_get_main_queue(), ^{
    NSLog(@"1111111111");
});
```

[附：我的博客地址](https://gsl201600.github.io/2019/02/02/iOS%E6%AF%8F%E9%9A%94%E4%B8%80%E6%AE%B5%E6%97%B6%E9%97%B4%E6%89%A7%E8%A1%8C%E4%B8%80%E6%AC%A1%E4%BB%A3%E7%A0%81/)

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

# iOS 模态跳转推出透明背景方法

**1. OS >= iOS 8.0**

```
FZLoginViewController *fzLoginViewController = [[FZLoginViewController alloc] init];
fzLoginViewController.modalPresentationStyle = UIModalPresentationOverCurrentContext;
[[UIApplication sharedApplication].keyWindow.rootViewController presentViewController:fzLoginViewController animated:NO completion:nil];
```

**2. 若系统需兼容7.0 需要加处理**

```
if ([[UIDevice currentDevice].systemVersion floatValue] >= 8.0) {
    controller.modalPresentationStyle = UIModalPresentationOverCurrentContext;
    [self presentViewController:controller animated:YES completion:nil];
} else {
    self.view.window.rootViewController.modalPresentationStyle = UIModalPresentationCurrentContext;
    [self presentViewController:controller animated:NO completion:nil];
    self.view.window.rootViewController.modalPresentationStyle = UIModalPresentationFullScreen;
}
```

[附：我的博客地址](https://gsl201600.github.io/2019/03/04/iOS%E6%A8%A1%E6%80%81%E8%B7%B3%E8%BD%AC%E6%8E%A8%E5%87%BA%E9%80%8F%E6%98%8E%E8%83%8C%E6%99%AF%E6%96%B9%E6%B3%95/)

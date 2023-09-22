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

# iOS 快速定位到系统设置界面

```
//定位服务设置界面
NSURL * url = [NSURL URLWithString:@"prefs:root=LOCATION_SERVICES"];
if ([[UIApplication sharedApplication] canOpenURL:url]) {
    [[UIApplication sharedApplication] openURL:url];
}

//定位服务设置界面
@"prefs:root=LOCATION_SERVICES"
//GameCenter设置界面
@"prefs:root=GAME_CENTER"
//WIFI设置界面
@"prefs:root=WIFI"
//通知设置界面
@"prefs:root=NOTIFICATIONS_ID"
//蓝牙设置界面
@"prefs:root=Bluetooth"
//iCloud设置界面
@"prefs:root=CASTLE"

//调用系统邮件发邮件
- (void)gotoEmail:(NSString *)emailAccount{
    NSString * recipients = [NSString stringWithFormat:@"mailto:%@", emailAccount];
    NSString * email = [recipients stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding];
    [[UIApplication sharedApplication] openURL:[NSURL URLWithString:email]];
}
```

[附：我的博客地址](https://gsl201600.github.io/2019/02/01/iOS%E5%BF%AB%E9%80%9F%E5%AE%9A%E4%BD%8D%E5%88%B0%E7%B3%BB%E7%BB%9F%E8%AE%BE%E7%BD%AE%E7%95%8C%E9%9D%A2/)

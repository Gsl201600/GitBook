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

# iOS 给图片加水印

```
// 给图片加水印
- (void)addWatermarkToPicture{
    // 获取原图片
    UIImage *image = [UIImage imageNamed:@"FZSDKLib.bundle/001.jpg"];
    // 计算图片的size
    CGSize imageSize = CGSizeMake(image.size.width, image.size.height);
    // 开启图片类型的图形上下文
    UIGraphicsBeginImageContextWithOptions(imageSize, NO, 0);
    // 绘制图片
    [image drawAtPoint:CGPointZero];
    // 水印的文字
    NSString *str = @"水印文字";
    // 绘制文字水印
    [str drawAtPoint:CGPointMake(30, 30) withAttributes:@{NSFontAttributeName:[UIFont systemFontOfSize:20], NSForegroundColorAttributeName:[UIColor redColor]}];
    // 水印的图片
    UIImage *logoImage = [UIImage imageNamed:@"FZSDKLib.bundle/companyLogo"];
    // 绘制图片水印
    [logoImage drawAtPoint:CGPointMake(imageSize.width - logoImage.size.width - 30, imageSize.height - logoImage.size.height - 30)];
    // 取图片
    image = UIGraphicsGetImageFromCurrentImageContext();
    // 关闭图片类型的图形上下文
    UIGraphicsEndImageContext();
    // 保存到相册中
    UIImageWriteToSavedPhotosAlbum(image, NULL, NULL, NULL);
}
```

[附：我的博客地址](https://gsl201600.github.io/2019/01/30/iOS%E7%BB%99%E5%9B%BE%E7%89%87%E5%8A%A0%E6%B0%B4%E5%8D%B0/)

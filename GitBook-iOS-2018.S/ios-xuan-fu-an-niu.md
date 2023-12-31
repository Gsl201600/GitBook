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

# iOS 悬浮按钮

新建继承于UIWindow的类 .h文件如下

```
typedef void(^ClickBlock)(NSInteger i);

@interface GSLFloatingView : UIWindow

@property (nonatomic, copy) ClickBlock clickBlock;

//重要：所有图片都要是圆形的，程序里并没有自动处理成圆形
//  warning: frame的长宽必须相等
- (instancetype)initWithFrame:(CGRect)frame mainImageName:(NSString *)mainImageName imagesAndTitle:(NSDictionary *)imagesAndTitle backgroundColor:(UIColor *)bgColor;

// 显示（默认）
- (void)showWindow;

// 隐藏
- (void)dissmissWindow;
```

.m 实现文件

```
#import "GSLFloatingView.h"

#define WIDTH self.frame.size.width
#define HEIGHT self.frame.size.height
#define kScreenWidth [UIScreen mainScreen].bounds.size.width
#define kScreenHeight [UIScreen mainScreen].bounds.size.height

#define animateDuration 0.3       //位置改变动画时间
#define showDuration 0.1          //展开动画时间
#define statusChangeDuration  3.0    //状态改变时间
#define normalAlpha  0.8           //正常状态时背景alpha值
#define sleepAlpha  0.3           //隐藏到边缘时的背景alpha值
#define myBorderWidth 1.0         //外框宽度
#define marginWith  5             //间隔

@interface GSLFloatingView ()

@property (nonatomic, assign) CGFloat frameWidth;
@property (nonatomic, assign) BOOL isShowTab;
@property (nonatomic, strong) UIPanGestureRecognizer *panGesture;
@property (nonatomic, strong) UITapGestureRecognizer *tapGesture;
@property (nonatomic, strong) UIButton *mainImageButton;
@property (nonatomic, strong) UIView *contentView;
@property (nonatomic, strong) NSDictionary *imagesAndTitle;
@property (nonatomic, strong) UIColor *bgColor;

@end

@implementation GSLFloatingView

- (instancetype)initWithFrame:(CGRect)frame mainImageName:(NSString *)mainImageName imagesAndTitle:(NSDictionary *)imagesAndTitle backgroundColor:(UIColor *)bgColor{
    if (self = [super initWithFrame:frame]) {
        //  断言NSAssert()是一个宏，用于开发阶段调试程序中的Bug，通过为NSAssert()传递条件表达式来断定是否属于Bug，满足条件返回真值，程序继续运行，如果返回假值，则抛出异常，并且可以自定义异常描述。
        //  Xcode 已经默认将release环境下的断言取消了, 免除了忘记关闭断言造成的程序不稳定. 所以不用担心 在开发时候大胆使用。
        NSAssert(mainImageName != nil, @"mainImageName can't be nil !");
        NSAssert(imagesAndTitle != nil, @"imagesAndTitle can't be nil !");
        _isShowTab = NO;
        
        self.backgroundColor = [UIColor clearColor];
        self.windowLevel = UIWindowLevelAlert + 1;
        self.rootViewController = [[UIViewController alloc] init];
        [self makeKeyAndVisible];
        
        _bgColor = bgColor;
        _frameWidth = frame.size.width;
        _imagesAndTitle = imagesAndTitle;
        
        _contentView = [[UIView alloc] initWithFrame:CGRectMake(_frameWidth, 0, imagesAndTitle.count * (_frameWidth + 5), _frameWidth)];
        _contentView.alpha = 0;
        [self addSubview:_contentView];
        //添加按钮
        [self setButtons];
        
        _mainImageButton = [UIButton buttonWithType:UIButtonTypeCustom];
        _mainImageButton.frame = CGRectMake(0, 0, frame.size.width, frame.size.height);
        [_mainImageButton setImage:[UIImage imageNamed:mainImageName] forState:UIControlStateNormal];
        _mainImageButton.alpha = sleepAlpha;
        [_mainImageButton addTarget:self action:@selector(mainButtonDidClicked:) forControlEvents:UIControlEventTouchUpInside];
        [self addSubview:_mainImageButton];
        
        [self doBorderWidth:myBorderWidth color:nil cornerRadius:_frameWidth/2];
        
        _panGesture = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(locationChange:)];
        _panGesture.delaysTouchesBegan = NO;
        [self addGestureRecognizer:_panGesture];
        
        _tapGesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(mainButtonDidClicked:)];
        [self addGestureRecognizer:_tapGesture];
        
        //设备旋转的时候收回按钮
        [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(orientChange:) name:UIApplicationDidChangeStatusBarOrientationNotification object:nil];
    }
    return self;
}

// 显示（默认）
- (void)showWindow{
    self.hidden = NO;
}

// 隐藏
- (void)dissmissWindow{
    self.hidden = YES;
}

- (void)setButtons{
    int i = 0;
    for (NSString *key in _imagesAndTitle) {
        UIButton *button = [UIButton buttonWithType:UIButtonTypeCustom];
        button.frame = CGRectMake(self.frameWidth * i, 0, self.frameWidth, self.frameWidth);
        button.backgroundColor = [UIColor clearColor];
        
        UIImage *image = [UIImage imageNamed:key];
        [button setTitle:_imagesAndTitle[key] forState:UIControlStateNormal];
        [button setImage:image forState:UIControlStateNormal];
        
        button.tag = i;
        
        // 则默认image在左，title在右
        // 改成image在上，title在下
        button.titleEdgeInsets = UIEdgeInsetsMake(self.frameWidth/2, -image.size.width, 0.f, 0.f);
        button.imageEdgeInsets = UIEdgeInsetsMake(2.f, 8.f, 16.f, -button.titleLabel.bounds.size.width + 8);
        button.titleLabel.font = [UIFont systemFontOfSize:self.frameWidth/5];
        [button addTarget:self action:@selector(itemsDidClicked:) forControlEvents:UIControlEventTouchUpInside];
        [self.contentView addSubview:button];
        i++;
    }
}

#pragma mark  ------- 绘图操作 ----------
- (void)drawRect:(CGRect)rect {
    [self drawDash];
}

//分割线
- (void)drawDash{
    CGContextRef context = UIGraphicsGetCurrentContext();
    CGContextBeginPath(context);
    CGContextSetLineWidth(context, 0.1);
    CGContextSetStrokeColorWithColor(context, [UIColor whiteColor].CGColor);
    CGFloat lengths[] = {2, 1};
    CGContextSetLineDash(context, 0, lengths, 2);
    for (int i = 1; i < _imagesAndTitle.count; i++) {
        CGContextMoveToPoint(context, self.contentView.frame.origin.x + i * self.frameWidth, marginWith * 2);
        CGContextAddLineToPoint(context, self.contentView.frame.origin.x + i * self.frameWidth, self.frameWidth - marginWith * 2);
    }
    CGContextStrokePath(context);
}

#pragma mark ------- contentview 操作 --------------------
//按钮在屏幕右边时，左移contentview
- (void)moveContentviewLeft{
    _contentView.frame = CGRectMake(self.frameWidth/3, 0, _contentView.frame.size.width, _contentView.frame.size.height);
}

//按钮在屏幕左边时，contentview恢复默认
- (void)resetContentview{
    _contentView.frame = CGRectMake(self.frameWidth + marginWith, 0, _contentView.frame.size.width, _contentView.frame.size.height);
}

//改变位置
- (void)locationChange:(UIPanGestureRecognizer*)panGesture{
    CGPoint panPoint = [panGesture locationInView:[UIApplication sharedApplication].keyWindow];
    if (panGesture.state == UIGestureRecognizerStateBegan) {
        [NSObject cancelPreviousPerformRequestsWithTarget:self selector:@selector(changeStatus) object:nil];
        _mainImageButton.alpha = normalAlpha;
    }else if (panGesture.state == UIGestureRecognizerStateChanged) {
        self.center = CGPointMake(panPoint.x, panPoint.y);
    }else if (panGesture.state == UIGestureRecognizerStateEnded){
        [self performSelector:@selector(changeStatus) withObject:nil afterDelay:statusChangeDuration];
        if (panPoint.x <= kScreenWidth/2) {
            if (panPoint.y <= 40 + HEIGHT/2 && panPoint.x >= 20 + WIDTH/2) {
                [UIView animateWithDuration:animateDuration animations:^{
                    self.center = CGPointMake(panPoint.x, HEIGHT/2);
                }];
            }else if (panPoint.y >= kScreenHeight - HEIGHT/2 - 40 && panPoint.x >= 20 +WIDTH/2){
                [UIView animateWithDuration:animateDuration animations:^{
                    self.center = CGPointMake(panPoint.x, kScreenHeight-HEIGHT/2);
                }];
            }else if (panPoint.x < WIDTH/2 + 20 && panPoint.y > kScreenHeight - HEIGHT/2){
                [UIView animateWithDuration:animateDuration animations:^{
                    self.center = CGPointMake(WIDTH/2, kScreenHeight-HEIGHT/2);
                }];
            }else{
                CGFloat pointy = panPoint.y < HEIGHT/2 ? HEIGHT/2 :panPoint.y;
                [UIView animateWithDuration:animateDuration animations:^{
                    self.center = CGPointMake(WIDTH/2, pointy);
                }];
            }
        }else if (panPoint.x > kScreenWidth/2){
            if (panPoint.y <= 40 + HEIGHT/2 && panPoint.x < kScreenWidth - WIDTH/2 - 20) {
                [UIView animateWithDuration:animateDuration animations:^{
                    self.center = CGPointMake(panPoint.x, HEIGHT/2);
                }];
            }else if (panPoint.y >= kScreenHeight - 40 - HEIGHT/2 && panPoint.x < kScreenWidth - WIDTH/2 - 20){
                [UIView animateWithDuration:animateDuration animations:^{
                    self.center = CGPointMake(panPoint.x, kScreenHeight-HEIGHT/2);
                }];
            }else if (panPoint.x > kScreenWidth - WIDTH/2 - 20 && panPoint.y < HEIGHT/2){
                [UIView animateWithDuration:animateDuration animations:^{
                    self.center = CGPointMake(kScreenWidth-WIDTH/2, HEIGHT/2);
                }];
            }else{
                CGFloat pointy = panPoint.y > kScreenHeight-HEIGHT/2 ? kScreenHeight-HEIGHT/2 :panPoint.y;
                [UIView animateWithDuration:animateDuration animations:^{
                    self.center = CGPointMake(kScreenWidth-WIDTH/2, pointy);
                }];
            }
        }
    }
}

//点击事件
- (void)mainButtonDidClicked:(UITapGestureRecognizer*)tapGesture{
    _mainImageButton.alpha = normalAlpha;
    
    //拉出悬浮窗
    if (self.center.x == 0) {
        self.center = CGPointMake(WIDTH/2, self.center.y);
    }else if (self.center.x == kScreenWidth){
        self.center = CGPointMake(kScreenWidth - WIDTH/2, self.center.y);
    }else if (self.center.y == 0){
        self.center = CGPointMake(self.center.x, HEIGHT/2);
    }else if (self.center.y == kScreenHeight){
        self.center = CGPointMake(self.center.x, kScreenHeight - HEIGHT/2);
    }
    //展示按钮列表
    if (!self.isShowTab) {
        self.isShowTab = YES;
        //为了主按钮点击动画
        self.layer.masksToBounds = YES;
        
        [UIView animateWithDuration:showDuration animations:^{
            _contentView.alpha = 1;
            if (self.frame.origin.x <= kScreenWidth/2) {
                [self resetContentview];
                self.frame = CGRectMake(self.frame.origin.x, self.frame.origin.y, WIDTH + _imagesAndTitle.count * (self.frameWidth + marginWith), self.frameWidth);
            }else{
                [self moveContentviewLeft];
                self.mainImageButton.frame = CGRectMake((_imagesAndTitle.count * (self.frameWidth + marginWith)), 0, self.frameWidth, self.frameWidth);
                self.frame = CGRectMake(self.frame.origin.x - _imagesAndTitle.count * (self.frameWidth + marginWith), self.frame.origin.y, (WIDTH + _imagesAndTitle.count * (self.frameWidth + marginWith)), self.frameWidth);
            }
            if (_bgColor) {
                self.backgroundColor = _bgColor;
            }else{
                self.backgroundColor = [UIColor grayColor];
            }
        }];
        
        //移除pan手势
        if (_panGesture) {
            [self removeGestureRecognizer:_panGesture];
        }
        [NSObject cancelPreviousPerformRequestsWithTarget:self selector:@selector(changeStatus) object:nil];
    }else{
        self.isShowTab = NO;
        //为主按钮点击动画
        self.layer.masksToBounds = NO;
        //添加pan手势
        if (_panGesture) {
            [self addGestureRecognizer:_panGesture];
        }
        [UIView animateWithDuration:showDuration animations:^{
            _contentView.alpha = 0;
            if (self.frame.origin.x + self.mainImageButton.frame.origin.x <= kScreenWidth/2) {
                self.frame = CGRectMake(self.frame.origin.x, self.frame.origin.y, self.frameWidth, self.frameWidth);
            }else{
                self.mainImageButton.frame = CGRectMake(0, 0, self.frameWidth, self.frameWidth);
                self.frame = CGRectMake(self.frame.origin.x + _imagesAndTitle.count * (self.frameWidth + marginWith), self.frame.origin.y, self.frameWidth, self.frameWidth);
            }
            self.backgroundColor = [UIColor clearColor];
        }];
        [self performSelector:@selector(changeStatus) withObject:nil afterDelay:statusChangeDuration];
    }
}

- (void)changeStatus{
    [UIView animateWithDuration:1.0 animations:^{
        _mainImageButton.alpha = sleepAlpha;
    }];
    [UIView animateWithDuration:0.5 animations:^{
        CGFloat x = self.center.x < 20+WIDTH/2 ? 0 :  self.center.x > kScreenWidth - 20 -WIDTH/2 ? kScreenWidth : self.center.x;
        CGFloat y = self.center.y < 40 + HEIGHT/2 ? 0 : self.center.y > kScreenHeight - 40 - HEIGHT/2 ? kScreenHeight : self.center.y;
        
        //禁止停留在4个角
        if((x == 0 && y ==0) || (x == kScreenWidth && y == 0) || (x == 0 && y == kScreenHeight) || (x == kScreenWidth && y == kScreenHeight)){
            y = self.center.y;
        }
        self.center = CGPointMake(x, y);
    }];
}

- (void)doBorderWidth:(CGFloat)width color:(UIColor *)color cornerRadius:(CGFloat)cornerRadius{
    self.layer.cornerRadius = cornerRadius;
    self.layer.borderWidth = width;
    if (!color) {
        self.layer.borderColor = [UIColor whiteColor].CGColor;
    }else{
        self.layer.borderColor = color.CGColor;
    }
}

#pragma mark  ------- button事件 ---------
- (void)itemsDidClicked:(id)sender{
    if (self.isShowTab){
        [self mainButtonDidClicked:nil];
    }
    
    UIButton *button = (UIButton *)sender;
    if (self.clickBlock) {
        self.clickBlock(button.tag);
    }
}

#pragma mark  ------- 设备旋转 -----------
- (void)orientChange:(NSNotification *)notification{
    //不设置的话,长按动画那块有问题
    self.layer.masksToBounds = YES;
    
    //旋转前要先改变frame，否则坐标有问题（临时办法）
    self.frame = CGRectMake(0, kScreenHeight - self.frame.origin.y - self.frame.size.height, self.frame.size.width,self.frame.size.height);
    
    if (self.isShowTab) {
        [self mainButtonDidClicked:nil];
    }
}

@end
```

[附：我的博客地址](https://gsl201600.github.io/2019/02/19/iOS%E6%82%AC%E6%B5%AE%E6%8C%89%E9%92%AE/)

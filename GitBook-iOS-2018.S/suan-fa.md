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

# 算法

**1. 不用中间变量,用两种方法交换A和B的值**

```
// 1.中间变量
void swap(int a, int b) {
   int temp = a;
   a = b;
   b = temp;
}

// 2.加法
void swap(int a, int b) {
   a = a + b;
   b = a - b;
   a = (a - b)/2;
}

// 3.异或（相同为0，不同为1. 可以理解为不进位加法）
void swap(int a, int b) {
   a = a ^ b;
   b = a ^ b;
   a = a ^ b;
}
```

**2. 求最大公约数**

```
/** 1.直接遍历法 */
int maxCommonDivisor(int a, int b) {
    int max = 0;
    for (int i = 1; i <= b; i++) {
        if (a % i == 0 && b % i == 0) {
            max = i;
        }
    }
    return max;
}
/** 2.辗转相除法 */
int maxCommonDivisor(int a, int b) {
    int r;
    while(a % b > 0) {
        r = a % b;
        a = b;
        b = r;
    }
    return b;
}
// 扩展：最小公倍数 = (a * b)/最大公约数
```

**3. 排序算法**

> 选择排序、冒泡排序、插入排序三种排序算法可以总结为如下： 都将数组分为已排序部分和未排序部分。 1). 选择排序将已排序部分定义在左端，然后选择未排序部分的最小元素和未排序部分的第一个元素交换。 2). 冒泡排序将已排序部分定义在右端，在遍历未排序部分的过程执行交换，将最大元素交换到最右端。 3). 插入排序将已排序部分定义在左端，将未排序部分元的第一个元素插入到已排序部分合适的位置。

* 选择排序

```
NSMutableArray *selectArr = [NSMutableArray arrayWithArray:@[@"13",@"5",@"89",@"20",@"40",@"18",@"120"]];
for (int i = 0; i < selectArr.count - 1; i++) {
    for (int j = i + 1; j < selectArr.count; j++) {
        if ([selectArr[i] integerValue] > [selectArr[j] integerValue]) {
            NSString *temp = selectArr[i];
            selectArr[i] = selectArr[j];
            selectArr[j] = temp;
        }
    }
}

//【选择排序】最值出现在起始端
void selectSort(int *arr, int length) {
    for (int i = 0; i < length - 1; i++) { //趟数
        for (int j = i + 1; j < length; j++) { //比较次数
            if (arr[i] > arr[j]) {
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }
    }
}
```

* 冒泡排序

```
NSMutableArray *dataArr = [NSMutableArray arrayWithArray:@[@"13",@"5",@"89",@"20",@"40",@"18",@"120"]];
for (int i = 0; i < dataArr.count - 1; i++) {
    for (int j = 0; j < dataArr.count - 1 - i; j++) {
        if ([dataArr[j] integerValue] > [dataArr[j+1] integerValue]) {
            NSString *temp = dataArr[j];
            dataArr[j] = dataArr[j+1];
            dataArr[j+1] = temp;
        }
    }
}

//【冒泡排序】相邻元素两两比较，比较完一趟，最值出现在末尾
void bublleSort(int *arr, int length) {
    for(int i = 0; i < length - 1; i++) { //趟数
        for(int j = 0; j < length - i - 1; j++) { //比较次数
            if(arr[j] > arr[j+1]) {
                int temp = arr[j];
                arr[j] = arr[j+1];
                arr[j+1] = temp;
            }
        } 
    }
}
```

* 插入排序

```
// 第一步我们取出第二数 然后让 第二个数与第一个数比较 如果 第二个数< 第一个数  那么我们就将数组第二个数的位子换成第一个数的值 然后 j--
NSMutableArray *insertArr = [NSMutableArray arrayWithArray:@[@"13",@"5",@"89",@"20",@"40",@"18",@"120"]];
for (int i = 1; i < insertArr.count; i++) {
    int j = i;
    NSString *temp = insertArr[i];
    while (j > 0 && [insertArr[j] integerValue] < [insertArr[j-1] integerValue]) {
        [insertArr replaceObjectAtIndex:j withObject:insertArr[j-1]];
        j--;
    }
    [insertArr replaceObjectAtIndex:j withObject:temp];
}
```

* 希尔排序

```
// 类似于对半查找 但又不一样
NSMutableArray * hillArr = [NSMutableArray arrayWithArray:@[@"13",@"5",@"89",@"20",@"40",@"18",@"120"]];
int gap = hillArr.count/2;
while (gap >= 1) {
    for (int i = gap; i < hillArr.count; i++) {
        NSString *temp = hillArr[i];
        int j = i;
        while (j >= gap && [temp integerValue] < [hillArr[j-gap] integerValue]) {
            [hillArr replaceObjectAtIndex:j withObject:hillArr[j-gap]];
            j -=gap;
        }
        [hillArr replaceObjectAtIndex:j withObject:temp];
    }
    gap = gap/2;
}
```

* 快排

```
- (void)qkSortArr:(NSMutableArray *)kArr low:(NSInteger)low hight:(NSInteger)hight{
    if (low >= hight) {
        return;
    }
    NSInteger i = low;
    NSInteger j = hight;
    NSString *temp = kArr[low];
    
    while (i < j) {
        while (i < j && [temp integerValue] <= [kArr[j] integerValue]) {
            j--;
        }
        kArr[i] = kArr[j];
        
        while (i < j && [temp integerValue] >= [kArr[i] integerValue]) {
            i++;
        }
        kArr[j] = kArr[i];
    }
    
    kArr[i] = temp;
    
    [self qkSortArr:kArr low:low hight:i-1];
    [self qkSortArr:kArr low:i+1 hight:hight];
}
```

**4. 折半查找（二分查找）**

> 折半查找：优化查找时间（不用遍历全部数据） 折半查找的原理： 1). 数组必须是有序的 2). 必须已知min和max（知道范围） 3). 动态计算mid的值，取出mid对应的值进行比较 4). 如果mid对应的值大于要查找的值，那么max要变小为mid-1 5). 如果mid对应的值小于要查找的值，那么min要变大为mid+1

```
- (NSInteger)binarySearchArr:(NSArray *)arr withLow:(NSInteger)low withHight:(NSInteger)hight withSelcetID:(NSString *)key{
    if (low < hight) {
        NSInteger mid = (low + hight)/2;
        if ([key integerValue] == [arr[mid] integerValue]) {
            return mid;
        }else if ([key integerValue] < [arr[mid] integerValue]){
            return [self binarySearchArr:arr withLow:low withHight:mid-1 withSelcetID:key];
        }else{
            return [self binarySearchArr:arr withLow:mid+1 withHight:hight withSelcetID:key];
        }
    }else{
        NSLog(@"参数错误");
        return -1;
    }
}

// 已知一个有序数组, 和一个key, 要求从数组中找到key对应的索引位置 
int findKey(int *arr, int length, int key) {
    int min = 0, max = length - 1, mid;
    while (min <= max) {
        mid = (min + max) / 2; //计算中间值
        if (key > arr[mid]) {
            min = mid + 1;
        } else if (key < arr[mid]) {
            max = mid - 1;
        } else {
            return mid;
        }
    }
    return -1;
}
```

[附：我的博客地址](https://gsl201600.github.io/2019/01/09/%E7%AE%97%E6%B3%95/)

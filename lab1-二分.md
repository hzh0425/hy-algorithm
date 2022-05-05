# 二分

## 1. 二分模板

任何题目,只要想用二分做,就可以套用以下两个模板之一。所谓自古真情留不住,全靠模板留人心

因此,不要再去考虑什么<=  >= ,还是left = mid + 1,还是right = mid -1

这两个模板唯一的区别在于,mid到底划分给左半区间,还是右半区间

如果mid划分给左边区间,也即right = mid ,则用模板一

否则用模板二

实际很多的二分题目,mid划分给哪个区间都可以,为了方便更多的用模板一

当然有一部分二分查找的题目,是需要好好考虑mid给哪一边的

![image-20210714095449794](https://i.loli.net/2021/07/16/Mw6H92LaJXY5dGv.png)

## 2.二分步骤

首先要明确的一点,上面的两个模板,一定是退出循环后检查答案,而不是在循环体内返回答案

- 首先,也是最重要的,画图,思考题目要求查找的点是什么?
- 接着,我们要去寻找这个点,就需要设计一个check函数,使得让原序列满足二段性,每次剪掉一半不满足这个性质的区间,去满足性质的另一个区间寻找这个点。最后,当区间的长度变为一,也即left = right 的时候,就会退出循环
- 编写模板,考虑mid划分给哪一边
- 最后,退出循环,检查是否是我们想要的答案



## 3.题目集

### 3.1 简单题

#### [1.第一个错误的版本](https://leetcode-cn.com/problems/first-bad-version/)

![](https://i.loli.net/2021/07/16/2DOiTLVHKU6NJSR.png)

按照二分步骤:

- 要搜索的点是什么 -- 第一个错误的版本
- 设计check函数 -- 也即题目给的isBadVersion(mid) ,如果返回true,说明答案就在mid的左边,但是mid也有可能是答案,所以应该到左边区间去寻找(right = mid) , 否则就是相反 , 到右边寻找(left = mid + 1)
- 最后退出循环体,返回left 或者 right 都可以

套用模板一 :

```c++
class Solution {
public:
int firstBadVersion(int n) {
    if (n == 1) return 1;
    int lo = 1 , hi = n;
    while (lo < hi) {
        int  mid = (lo + (long long) hi) >> 1; //注意可能会溢出
        if (isBadVersion(mid)) {
            hi = mid;
        } else {
            lo = mid + 1;
        }
    }
    return lo;
}
};
```

#### [2.x 的平方根](https://leetcode-cn.com/problems/sqrtx/)

![image-20210716114111043](https://i.loli.net/2021/07/16/OkBrpc5eUIdR1Sl.png)

按照二分步骤:

- 要搜索的点是什么 -- x的平方根 , 也即最大的整数,满足 其平方 <= x
- 设计check函数 --  对于mid , 如果其满足 mid * mid <= x,说明答案就在mid的右边 , 但是mid也有可能是答案,所以去右边区间寻找 (left = mid , 也即要套用模板二) , 否则左边区间寻找(right = mid - 1);
- 最后退出循环体,返回left 或者 right 都可以

套用模板二 :

```
class Solution {
public:
int mySqrt(int x) {
    int left = 0 , right = x;
    while (left < right) {
        long long mid  = (left + (long long)right + 1) >> 1; //防止溢出
        if (mid <= x / mid) {
            //整数相乘可能会溢出,所以应该换乘除法
            left = mid;
        } else {
            right = mid - 1;
        }
    }
    return left;
}
};
```

#### [3.搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)

![image-20210716114444809](https://i.loli.net/2021/07/16/NejoYMd2HcUnfK6.png)

- 要搜索的点是什么 -- 要搜索一个位置 , 如果这个元素存在 , 就返回这个元素的下标 , 否则返回其能够插入的位置 , 总结起来就是,找到第一个>=x的下标
- 设计check函数 --  对于mid , 如果满足nums[mid] >= x ,说明要去左边区间寻找,并且mid也可能是答案 (也即right = mid)
- 最后退出循环体,返回left 或者 right 都可以

套用模板一 :

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        // 边界问题,如果整个数组的最大值 < target ,则直接返回最后一个下标即可
        if (nums.empty() || nums.back() < target) return nums.size();
        int left = 0 , right = nums.size() - 1;
        while (left < right) {
            int mid = (left + right) >> 1;
            if (nums[mid] >= target) {
                right = mid;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
};
```

### 3.2 中等题

#### [1.在排序数组中查找第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

![image-20210717091737368](https://i.loli.net/2021/07/17/qf4PQ6hy8jnt2iY.png)

- 要搜索的点是什么 -- 目标元素的左右端点，
- 设计check函数 --  对于左端点，相当于寻找第一个 >= target的位置 ， 对于 右端点，相当于寻找第一个 <= target的位置，所以可以分两次二分，分别寻找左右端点即可

```c++
class Solution {
public:
vector<int> searchRange(vector<int>& nums, int target) {
    vector<int> result = {-1,-1};
    if (nums.size() == 0)return result;
    //1.寻找左边界
    int lo = 0, hi = nums.size() - 1;
    while (lo < hi) {
        int mid = (lo + hi) >> 1;
        if (nums[mid] >= target) {
            hi = mid;
        } else {
            lo = mid + 1;
        }
    }
    // 有可能数不存在
    if (nums[lo] != target) return result;
    result [0] = lo;
    //2.寻找右边界
    lo = 0, hi = nums.size() - 1;
    while (lo < hi) {
        // 因为mid归到了右半部分,所以要+1;
        int mid = (lo + hi + 1) >> 1;
        if (nums[mid] <= target) {
            lo = mid;
        } else {
            hi = mid - 1;
        }
    }
    result [1] = lo;
    return result;
}
};
```

#### [2.寻找旋转排序数组中的最小值](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

![image-20210717092240350](https://i.loli.net/2021/07/17/C9jBJDWuamZN7cP.png)

- 要搜索的点是什么 -- 一个旋转排序数组中的最小值

- 设计check函数 --  考虑下面这幅图，一个数组在某个地方进行了旋转，很显然前半部分的数会严格大于最后一个数（图中的b）,

  而后半部分的数会严格小于b.我们想要寻找最小值，可以先判断当前的mid在哪一部分。如果在右半部分，则最小值肯定在Mid的左边。否则就在mid的右边

![image-20210717092645153](https://i.loli.net/2021/07/17/7CIcZlAvNdys1rn.png)

```c++
class Solution {
public:
int findMin(vector<int>& nums) {
    int lo = 0 , hi = nums.size() - 1;
    while (lo < hi) {
        int mid = (lo + hi) >> 1;
        //判断mid在哪一部分
        if (nums[mid] < nums.back()) {
            hi = mid;
        } else {
            lo = mid + 1;
        }
    }
    return nums[lo];
}
};
```

#### [3.搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

![image-20220505091920718](https://hzh-pic.oss-cn-beijing.aliyuncs.com/img/image-20220505091920718.png)

- 要搜索的点是什么 -- 一个旋转排序数组中的目标值

  我们现在无法知道目标值在左右哪一段。但是,可以先求出旋转数组中的最小值的下标 ， 然后判断目标值在哪一段，最后再来一次二分，在一个单调区间内搜索即可

```c++
class Solution {
public:
int search(vector<int>& nums, int target) {
    if (nums.empty()) return -1;
    int lo = 0 , hi = nums.size() - 1;
    while (lo < hi) {
        int mid = (lo + hi) >> 1;
        if (nums[mid] <= nums.back()) hi = mid ;
        else lo = mid + 1;
    }
    if (target <= nums.back()) {
        //如果target在右半部分，则在右半区间查找
        hi = nums.size() - 1;
    } else {
    	//否则，左半区间
        lo = 0 , hi --;
    }
    while (lo < hi) {
        int mid = (lo + hi) >> 1;
        if (nums[mid] >= target) hi = mid;
        else lo = mid + 1;
    }
    if (nums[lo] == target) return lo;
    else return -1;
}
};
```

### 3.3 挑战题3

#### [1.寻找峰值](https://leetcode-cn.com/problems/find-peak-element/)

![image-20220505092015797](https://hzh-pic.oss-cn-beijing.aliyuncs.com/img/image-20220505092015797.png)

- 要寻找的点 -- 一个峰值

- 设计check函数 -- 要找一个峰值，首先考虑峰值出现的条件是什么？比左右两边大，所以对于一个mid，如果nums[mid + 1] > nums[mid] , 则峰值一定会在右半区间出现，如下图所示

  否则，峰值就在左边

![image-20210717093722491](https://i.loli.net/2021/07/17/saOiqSD72FZWP8V.png)

```c++
class Solution {
public:
    int findPeakElement(vector<int>& nums) {
        int lo = 0, hi = nums.size() - 1;
        while (lo < hi) {
            int mid = (lo + hi) >> 1;
            if (nums[mid] < nums[mid + 1]) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        return lo;
    }
};
```

#### [2.寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)

![image-20210717094007850](https://i.loli.net/2021/07/17/mgXLYnyWCHhMGJe.png)

**抽屉原理介绍:**

桌上有十个苹果，要把这十个苹果放到九个抽屉里，无论怎样放，我们会发现至少会有一个抽屉里面放不少于两个苹果。这一现象就是我们所说的“抽屉原理”。 抽屉原理的一般含义为：“如果每个抽屉代表一个集合，每一个苹果就可以代表一个元素，假如有n+1个元素放到n个集合中去，其中必定有一个集合里至少有两个元素。” 抽屉原理有时也被称为鸽巢原理。它是组合数学中一个重要的原理 [1] 。

二分查找的思路是先猜一个数（有效范围 [left..right] 里位于中间的数 mid），然后统计原始数组中 小于等于 mid 的元素的个数 cnt：

如果 cnt 严格大于 mid。根据抽屉原理，重复元素就在区间 [left..mid] 里；
否则，重复元素就在区间 [mid + 1..right] 里。
与绝大多数使用二分查找问题不同的是，这道题正着思考是容易的，即：思考哪边区间存在重复数是容易的，因为有抽屉原理做保证。

时间复杂度：O(N log N)，二分法的时间复杂度为 O(log N)，在二分法的内部，执行了一次 for 循环，时间复杂度为 O(N)，故时间复杂度为 O(N log N)。

总结:有些题目给的序列虽然没有二分的单调性,但是可以不要拘束于题目的序列,可以把题目的序列看成一种工具

我们去利用这个工具,结合二分来做.一般二分的左右断点都是数组的左右断点下标,但是这题可以把二分的左右断点

看做边界的最小值和最大值

```
int findDuplicate(vector<int>& nums) {
    int size = nums.size();
    int lo = 1 , hi = size - 1;
    while (lo < hi) {
        int mid = (lo + hi) >> 1;
        int cnt = 0 ;
        for (auto k : nums) {
            if (k >= lo && k <=mid) {
                cnt ++ ;
            }
        }
        if (cnt > (mid - lo + 1)) {
            hi = mid;
        } else {
            lo = mid + 1;
        }
    }
    return lo ;
}
```














































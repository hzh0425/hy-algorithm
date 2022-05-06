# 一 原地修改数组

[26.删除排序数组中的重复项（简单）](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

[83.删除排序链表中的重复元素（简单）](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-list/)

[27.移除元素（简单）](https://leetcode-cn.com/problems/remove-element/)

[283.移动零（简单）](https://leetcode-cn.com/problems/move-zeroes/)

## [删除有序数组中的重复项](https://leetcode-cn.com/problems/remove-duplicates-from-sorted-array/)

先讲讲如何对一个有序数组去重，先看下题目：

![image-20210820113403560](https://gitee.com/zisuu/my-pic/raw/master/img/image-20210820113403560.png)

函数签名如下：



```
int removeDuplicates(int[] nums);
```

显然，由于数组已经排序，所以重复的元素一定连在一起，找出它们并不难，但如果毎找到一个重复元素就立即删除它，就是在数组中间进行删除操作，整个时间复杂度是会达到 O(N^2)。

简单解释一下什么是原地修改：

如果不是原地修改的话，我们直接 new 一个 `int[]` 数组，把去重之后的元素放进这个新数组中，然后返回这个新数组即可。

但是原地删除，不允许我们 new 新数组，只能在原数组上操作，然后返回一个长度，这样就可以通过返回的长度和原始数组得到我们去重后的元素有哪些了。

这种需求在数组相关的算法题中时非常常见的，**通用解法就是我们前文** [**双指针技巧**]() **中的快慢指针技巧**。

我们让慢指针 `slow` 走在后面，快指针 `fast` 走在前面探路，找到一个不重复的元素就告诉 `slow` 并让 `slow` 前进一步。这样当 `fast` 指针遍历完整个数组 `nums` 后，**`nums[0..slow]`** **就是不重复元素**。



```
int removeDuplicates(int[] nums) {
    if (nums.length == 0) {
        return 0;
    }
    int slow = 0, fast = 0;
    while (fast < nums.length) {
        if (nums[fast] != nums[slow]) {
            slow++;
            // 维护 nums[0..slow] 无重复
            nums[slow] = nums[fast];
        }
        fast++;
    }
    // 数组长度为索引 + 1
    return slow + 1;
}
```

看下算法执行的过程：

![image-20210820113410227](https://gitee.com/zisuu/my-pic/raw/master/img/image-20210820113410227.png)

再简单扩展一下，如果给你一个有序链表，如何去重呢？这是力扣第 83 题，其实和数组去重是一模一样的，唯一的区别是把数组赋值操作变成操作指针而已：



```
ListNode deleteDuplicates(ListNode head) {
    if (head == null) return null;
    ListNode slow = head, fast = head;
    while (fast != null) {
        if (fast.val != slow.val) {
            // nums[slow] = nums[fast];
            slow.next = fast;
            // slow++;
            slow = slow.next;
        }
        // fast++
        fast = fast.next;
    }
    // 断开与后面重复元素的连接
    slow.next = null;
    return head;
}
```



## 移除元素

这是力扣第 27 题，看下题目：

![image-20210820113432810](https://gitee.com/zisuu/my-pic/raw/master/img/image-20210820113432810.png)

函数签名如下：



```
int removeElement(int[] nums, int val);
```

题目要求我们把 `nums` 中所有值为 `val` 的元素原地删除，依然需要使用 [双指针技巧]() 中的快慢指针：

如果 `fast` 遇到需要去除的元素，则直接跳过，否则就告诉 `slow` 指针，并让 `slow` 前进一步。

这和前面说到的数组去重问题解法思路是完全一样的，就不画 GIF 了，直接看代码：



```
int removeElement(int[] nums, int val) {
    int fast = 0, slow = 0;
    while (fast < nums.length) {
        if (nums[fast] != val) {
            nums[slow] = nums[fast];
            slow++;
        }
        fast++;
    }
    return slow;
}
```

**注意这里和有序数组去重的解法有一个重要不同**，我们这里是先给 `nums[slow]` 赋值然后再给 `slow++`，这样可以保证 `nums[0..slow-1]` 是不包含值为 `val` 的元素的，最后的结果数组长度就是 `slow`。

## 移动零

这是力扣第 283 题，我来描述下题目：

给你输入一个数组 `nums`，请你**原地修改**，将数组中的所有值为 0 的元素移到数组末尾，函数签名如下：



```
void moveZeroes(int[] nums);
```

比如说给你输入 `nums = [0,1,4,0,2]`，你的算法没有返回值，但是会把 `nums` 数组原地修改成 `[1,4,2,0,0]`。

结合之前说到的几个题目，你是否有已经有了答案呢？

题目让我们将所有 0 移到最后，其实就相当于移除 `nums` 中的所有 0，然后再把后面的元素都赋值为 0 即可。

所以我们可以复用上一题的 `removeElement` 函数：



```
void moveZeroes(vector<int>& nums, int val) {
    int slow = 0, fast = 0;
    // 先把非0元素移到前面
    while (fast < nums.size()) {
        if (nums[fast] != 0) {
            nums[slow] = nums[fast];
            slow ++;
        }
        fast++;
    }
    // 置零后面的元素
    for (int i = slow; i < nums.size(); i++) {
        nums[i] = 0;
    }
}
```

至此，四道「原地修改」的算法问题就讲完了，其实核心还是快慢指针技巧，你学会了吗？



# 二 有序数组的平方

![image-20220506094543315](https://hzh-pic.oss-cn-beijing.aliyuncs.com/img/image-20220506094543315.png)

数组其实是有序的， 只不过负数平方之后可能成为最大数了。

那么数组平方的最大值就在数组的两端，不是最左边就是最右边，不可能是中间。

此时可以考虑双指针法了，i指向起始位置，j指向终止位置。

定义一个新数组result，和A数组一样的大小，让k指向result数组终止位置。

如果`A[i] * A[i] < A[j] * A[j]` 那么`result[k--] = A[j] * A[j];` 。

如果`A[i] * A[i] >= A[j] * A[j]` 那么`result[k--] = A[i] * A[i];` 。

如动画所示：

![img](https://code-thinking.cdn.bcebos.com/gifs/977.%E6%9C%89%E5%BA%8F%E6%95%B0%E7%BB%84%E7%9A%84%E5%B9%B3%E6%96%B9.gif)



```
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        int  i = 0, j = nums.size() - 1;
        vector<int> result(nums.size(), 0);
        int index = nums.size() - 1;
        while (i <= j) {
            if (nums[i] * nums[i] < nums[j] * nums[j]) {
                result[index --] = nums[j] * nums[j];
                j --;
            } else {
                result[index --] = nums[i] * nums[i];
                i ++;
            }
        }
        return result;
    }
};
```




























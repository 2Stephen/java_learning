[TOC]

# leetcode分类练习

## 一. 二分法

```java
// 通用解法
int le = 0, ri = nums.length - 1;
int mid;
while (le <= ri){
	mid = (le + ri) / 2;
	if(condition1)	// condition1即符合条件
		return mid;
	else if(condition2) le = mid + 1; // condition2即符合条件的部分在右半面
	else ri = mid - 1;	//符合条件的部分在左半面
}
```

### 1. [搜索插入位置](https://leetcode.cn/problems/search-insert-position/)（简单）

> 套用公式：
>
> condition1即 `nums里存在target` 或者 `nums中第i个数比target小第i + 1个数比target大，或者说第i个数就是最后一个数`
>
> 需要考虑特殊情况 就是target 比nums[0] 还小，那就在最后`return 0`即可

```java
public class Solution {
    public int searchInsert(int[] nums, int target) {
        int le = 0, ri = nums.length - 1;
        int mid;
        while (le <= ri){
            mid = (le + ri) / 2;
            if(nums[mid] == target) 
                return mid;
            else if(nums[mid] < target && (mid == nums.length - 1 || nums[mid +1] > target))
                return  mid + 1;
            else if(target > nums[mid]) le = mid + 1;
            else ri = mid - 1;
        }
        return 0;
    }
}
```

### 2. [搜索二维矩阵](https://leetcode.cn/problems/search-a-2d-matrix/)（中等）

>这道题有两种做法，第一种就是两次二分查找，第二种是一次二分查找
>
>两次二分查找：第一次根据首列元素确定target在哪一行，然后二分查找那一行即可，代码是重构了binarySearch函数（单纯因为懒得改，所以显得冗长）
>
>一次二分查找：我们获取所有元素个数，姑且设m行n列，所有元素个数即`m*n`，此时我们套用公式，找到mid，我们此时获得的mid只是元素个数的一半，需要找到mid在矩阵中的位置，即`matrix[mid / n][mid % n]`，然后就变成了单纯的二分查找（见公式）

```java
// 两次二分查找
public class Solution {
    int binarySearch(int[][] matrix, int target){
        int le = 0, ri = matrix.length - 1;
        int mid;
        while (le <= ri){
            mid = (le + ri) / 2;
            if(matrix[mid][0] <= target && (mid + 1 >= matrix.length || matrix[mid + 1][0] > target))
                return mid;
            if(matrix[mid][0] > target) ri = mid - 1;
            else le = mid + 1;
        }
        return -1;
    }
    boolean binarySearch(int[] matrix, int target){
        int le = 0, ri = matrix.length - 1;
        int mid;
        while (le <= ri){
            mid = (le + ri) / 2;
            if(matrix[mid] == target)
                return true;
            else if(matrix[mid] > target) ri = mid - 1;
            else le = mid + 1;
        }
        return false;
    }
    public boolean searchMatrix(int[][] matrix, int target) {
        int row = binarySearch(matrix, target);
        if(row == -1) return false;
        return binarySearch(matrix[row],target);
    }
}
```

```java
// 一次二分查找
public class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int m = matrix.length;
        int n = matrix[0].length;
        int le = 0, ri = n * m - 1;
        int mid;
        while(le <= ri){
            mid = (le + ri) >> 1;
            if(matrix[mid / n][mid % n] == target) return true;
            else if (matrix[mid / n][mid % n] > target) ri = mid - 1;
            else le = mid + 1;
        }
        return false;
    }
}
```

### 3. [寻找峰值](https://leetcode.cn/problems/find-peak-element/)（中等）

> 这道题不是严格递增的数组，所以二分查找还是有些难想的，我们可以把离散的数字连成线，画成图，纵坐标是数组每个数字的值，横坐标间隔1就行，我们每次二分时候取较高的那一面即可，正所谓“人往高处走”，至于为什么是这样，我们可以用一些极端的例子来考虑，如果就是一条直线，且递增，那如果我们取较小的那面就没有答案了，但是取较大的那一面，到最后就是答案（因为题目说边界设为负无穷）

```java
public class Solution {
    public int findPeakElement(int[] nums) {
        int len = nums.length;
        if(len == 1) return 0;
        if(nums[0] > nums[1])
            return 0;
        if(nums[len - 1] > nums[len - 2])
            return len - 1;
        int le = 1, ri = len - 2;
        int mid;
        while (le <= ri){
            mid = (le + ri) >> 1;
            if(nums[mid] > nums[mid + 1] && nums[mid] > nums[mid - 1]) return mid;
            else if(nums[mid] > nums[mid + 1]) ri = mid - 1;
            else le = mid + 1;
        }
        return 0;
    }
}
```

### 4. [搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)（中等）

> 这题还是二分法的变形，还可以套用公式模板，就是判断条件多了一点而已
>
> 建议做这题之前看一下上面的题，还是画图解决，每次二分必然有一半是连续的，有一半是不连续的（有断点），此时我们可以判断target在不在连续的那一面，如果target比连续的那面的最小值小或者比最大值大，那就在另一面了，或者我们可以这么想：对这两个不连续线段做y轴上的投影，我们可以发现，没有重合，所以我们只需判断在哪个区间即可

```java
public class Solution {
    public int search(int[] nums, int target) {
        int le = 0;
        int ri = nums.length - 1;
        int mid;
        while (le <= ri){
            mid = (le + ri)/2;
            if(target == nums[mid]) return mid;
            if(target == nums[le]) return le;
            if(target == nums[ri]) return ri;
            if(nums[mid] > nums[le]){
                if(target < nums[le] || target > nums[mid]) le = mid + 1;
                else ri = mid - 1;
            }else{
                if(target < nums[mid] || target > nums[ri]) ri = mid - 1;
                else le = mid + 1;
            }
        }
        return -1;
    }
}
```

### 5. [在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)（中等）

> 还是套用模板，我们需要进行两次二分查找，第一次查找相邻两个元素，第一个元素小于target，第二个元素等于target，这就找到了左边界
>
> 同理，第二次二分查找查询相邻两元素第一个等于target，第二个大于target
>
> 需要考虑边界情况，也就是`[1,2,3,4,4,4]`,`target = 4`，即一边碰壁的情况，防止数组越界

```java
class Solution {
    public int binarySearch1(int[] nums, int target){
        int le = 0;
        int ri = nums.length - 1;
        int mid;
        while (le <= ri){
            mid = (le + ri) / 2;
            if(nums[mid] == target && (mid == 0 || nums[mid - 1] < target)) return mid;
            else if(nums[mid] >= target) ri = mid - 1;
            else le = mid + 1;
        }
        return -1;
    }
    public int binarySearch2(int[] nums, int target){
        int le = 0;
        int ri = nums.length - 1;
        int mid;
        while (le <= ri){
            mid = (le + ri) / 2;
            if(nums[mid] == target && (mid == nums.length - 1 ||nums[mid + 1] > target)) return mid;
            else if(nums[mid] > target) ri = mid - 1;
            else le = mid + 1;
        }
        return -1;
    }
    public int[] searchRange(int[] nums, int target) {
        int[] ans = new int[2];
        ans[0] = binarySearch1(nums,target);
        ans[1] = binarySearch2(nums,target);
        return ans;
    }
}
```

### 6. [寻找旋转排序数组中的最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/)（中等）

> 这题和前面的一道题有点像，也是画图题，我们只需要把图画出来，就容易理解了。
>
> 只需要找断点即可，但是有一些小点需要特判：
>
> 首先我们对于第二次取值的le 和 ri 不能取 mid + 1或者mid - 1了，因为如果执行＋1或-1可能漏掉断点（其实在condition1特判一下也可以避免）
>
> 其次如果数组没旋转，那就返回首元素就行，至于判断是否旋转了，判断第一个元素和最后一个元素大小即可（还是做y轴的投影，因为左右两个线段在投影上是没有重合的，所以旋转过后必然后面的线段比前面所有线段都小）

```java
class Solution {
    public int findMin(int[] nums) {
        int le = 0;
        int ri = nums.length - 1;
        int mid;
        if(nums[le] <= nums[ri]) return nums[0];
        while (le <= ri){
            mid = (le + ri) / 2;
            if(nums[mid] > nums[mid + 1]) return nums[mid + 1];
            if(nums[le] < nums[mid]) le = mid;
            else ri = mid;
        }
        return nums[0];
    }
}
```


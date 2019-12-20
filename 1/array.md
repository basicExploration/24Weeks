# |必拿offer系列|算法|数组

## 数组中重复的数字
> 剑指offer

例如[11,2,4,6,3,2,1]

在一个长度为 n 的数组里的所有数字都在 0 到 n-1 的范围内。
数组中某些数字是重复的，但不知道有几个数字是重复的，
也不知道每个数字重复几次。请找出数组中任意一个重复的数字。
要求时间复杂度 O(N)，空间复杂度 O(1)

初步审题，如果不看时间复杂度和空间复杂度我的思路是使用一个map,然后map[int]int,只需要将map的value值大于1的直接输出即可。
但是这里无法这么使用，因为要求空间复杂度是o(1)

解题思路：题目中说明了所有的数据都在0 n-1之间 那么每个数据的大小都不会大于这个数组中所有的索引。因此为了防止使用额外的空间
我们的可以将int的每个值都放置在它数值代表的索引上。这样，如果发现第i个的位置上已经有了一个i大小的数字，那么就可以把这个重复的数字
放到要返回的数组中即可。

这道题的关键就是将数的位置移动到它实际大小的地方，这是个规则，但是你移动到实际大小-1也行，但是容易出现bug，边界问题，
移动到value大小的索引出就是为了标记数据而已，所以记住这种题的解决方法就是 **将数据移动到它大小的索引处即可**。
```go
func ArrayFindDuplicateNumbers(arr []int,d []int)bool{
	if len(arr) <= 0 || arr == nil {
		return false
	}
	for i := 0; i < len(arr); i++ {
		for arr[i] != i {// 这一步会直接找到究竟谁才是真的0（假设下标是0的情况下。）
			//注意虽然是两个for但是时间复杂度并不是n^2 ，每个数据最多只要交换两次就可以找到自己的位置。

			// 如果值跟要交换的值相等就不用交换了，直接输入到输出数组中即可。少了swap的时间了。
			if arr[i] == arr[arr[i]] {
				d[0] = arr[i]
				return true
			}
				swap(arr, arr[i], i)
		}

	}
	return false
}

// swap array's two data. 将数组中的两个数据进行位置交换
func swap(arr []int, i, j int) {
	arr[i], arr[j] = arr[j], arr[i]
}

```
---




## 二维数组中的查找
> [leetcode](https://leetcode-cn.com/problems/search-a-2d-matrix-ii/description/)

给定一个二维数组，其每一行从左到右递增排序，从上到下也是递增排序。给定一个数，判断这个数是否在该二维数组中

Consider the following matrix:

```go
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]

Given target = 5, return true.
Given target = 20, return false.
```

```go
func findArray(target int,arr [][]int)bool{
    if arr == nil || len(arr) ==0 || len(arr[0]) == 0 {
        return false
    }
    row,col := 0,len(arr[0])-1
    for row <= len(arr)-1 && col >=0 {
        fmt.Println(row,col,arr[row][col])
        if arr[row][col] == target {
            return true
        }else if arr[row][col] >target{
            col--
        }else {
            row++
        }
    }
    return false
}
```

这道题的关键就是从这个矩阵的右上角开始寻找，比它小往左走，比它大往下走，也就是说在每一个缩小的矩阵中，我们都用这种技巧，
就是每次arr[row][col]都是指的是最右上角的那个，用这种办法就可以将矩阵一点一点的消灭掉。

> 这个地方还可以使用二分法加遍历这样就加快速度了。
就是说每一层都用二分法，然后遍历外层就OK了。基本上时间复杂度是 `O(nlogm)`

---

## 移动零
> [leetcode](https://leetcode-cn.com/problems/move-zeroes/description/)

给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

示例:

输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
说明:

必须在原数组上操作，不能拷贝额外的数组。
尽量减少操作次数。

这道题是双指针问题无疑了，基本上数组的题都跟双指针分不开，基本上
双指针问题都要用到先排序，不过这个题不需要。
```go
func moveZeroes(nums []int)  {
    i,j := 0,0
    for j < len(nums){ 
        if nums[j] != 0 { //  注意这个是j j代表了全部的遍历过程 i代表了新的重组结构。
            nums[i] = nums[j]
            i++
        }
        j++
    }
    // 这个循环完毕以后，i的下下标豆薯属于非零的了，那么可以说i后面的下标都是0了，但是
    // 目前还没有。因为有一些内存地址还是其它值的，

    // 比如这样 [1,3,12,3,12]
    // 那么我们要做的就是把i后面的索引变成0就OK了。对吧对吧。
    for i < len(nums) {
        nums[i] = 0
        i++
    }
}
```

这道题的解题思路就是使用两个指针，然后j肯定是比i快的，最起码是一样快，所以不用担心i的内容会覆盖掉j，那么j前面的数据被i给覆盖了，
然后当覆盖到i结束，就是说0 - i 这个内存空间已经是正确的所有数据了，那么在i后面的数据尽管还有值，但是这些值其实已经被i这个指针提取到了前面
所以说i后面的索引值的内存空间是无用的，那么我们可以在i到len的这个空间内将数据变为0.

---
## 重塑矩阵
> [leetcode](https://leetcode-cn.com/problems/reshape-the-matrix/description/)

在MATLAB中，有一个非常有用的函数 reshape，它可以将一个矩阵重塑为另一个大小不同的新矩阵，但保留其原始数据。

给出一个由二维数组表示的矩阵，以及两个正整数r和c，分别表示想要的重构的矩阵的行数和列数。

重构后的矩阵需要将原始矩阵的所有元素以相同的行遍历顺序填充。

如果具有给定参数的reshape操作是可行且合理的，则输出新的重塑矩阵；否则，输出原始矩阵。

示例 1:

输入: 
```
nums = 
[[1,2],
 [3,4]]
r = 1, c = 4
输出: 
[[1,2,3,4]]
```
解释:
行遍历nums的结果是 [1,2,3,4]。新的矩阵是 1 * 4 矩阵, 用之前的元素值一行一行填充新矩阵。
示例 2:

输入: 
```bash
nums = 
[[1,2],
 [3,4]]
r = 2, c = 4
输出: 
[[1,2],
 [3,4]]
```
解释:
没有办法将 2 * 2 矩阵转化为 2 * 4 矩阵。 所以输出原矩阵。
注意：

给定矩阵的宽和高范围在 [1, 100]。
给定的 r 和 c 都是正数。
```go
func matrixReshape(nums [][]int, r int, c int) [][]int {
    if len(nums) * len(nums[0]) != r * c || r == 0 || c == 0 || len(nums) == 0 || len(nums[0]) == 0{
        return nums
    }

    result  :=  make([][]int,r)
    count,col := 0,len(nums[0])
    for i := range result { // 这两次的构造是按照新的数据结构去构造的。
        result[i] = make([]int,c)
        for j := range result[i] {
            result[i][j] = nums[count/col][count%col] 
	    
	    // 这里是关键,也就是说我们需要两个辅助的变量同时用到了小学数学里的商和余数。建议数学不好的同学，（例如我。。。）去补补小学数学。。
            count++ 
	    // 这里的++ 不需要进行限制，因为这个限制已经在j := range result[i] 这里进行了数量上的限制了。
        }
    }
    return result
}

```
这道题我有两个思路 一个就是 先构造新的result然后开始遍历这个result的数组，
然后再使用取商和取余数的方式把老数组的内容一个一个的取到新的数据结构中。

第二个思路 就是使用一个queue 然后把数据先导入进去，然后便利新的数组的时候一个一个加进去就OK了，
但是使用queue空间复杂度就大了，不如直接取余数和商。这种需要重新构造的数组一般取余数和取商是最好的方式了，
只需要使用一个一直增加的下标索引，然后再加上每个子数组的长度，就OK 了，这种取余的手法在循环stack queue的时候都有用到。
我感觉 这种取余的手法非常常见也非常重要

**下标跟数组长度取商可以发现是几倍于这个数组，取余可以发现是在这个数组长度的具体哪个索引处**

--- 

# 最大连续1的个数

> [leetcode](https://leetcode-cn.com/problems/max-consecutive-ones/description/)

给定一个二进制数组， 计算其中最大连续1的个数。

示例 1:

输入: [1,1,0,1,1,1]
输出: 3
解释: 开头的两位和最后的三位都是连续1，所以最大连续1的个数是 3.
注意：

输入的数组只包含 0 和1。
输入数组的长度是正整数，且不超过 10,000。



**解法一双指针解法**



```go

func findMaxConsecutiveOnes(nums []int) int {
    count := 0
    // 这里的count是一个计数值，是为了最后的输出。
    for i,j := 0,-1;i < len(nums);i++ { 
        
    // i ,j 在这种题中一般都是两个指针
    // 即所谓的数组双指针问题，一个指针是i一个是j，i是主指针主索引，j是辅助指针。

    // 在这道题中j是关键，i - j是关键，如果当nums[i] == 0的时候这个时候i - j就不能增加。所以这个时候j就要等于i，如果不是0的时候这个i - j的值就要增加所以这个时候i - j就要++ 所以这就是为什么最开始的时候j = -1 
    // 因为j要比i小一，才能在i - j的时候是增加的。

    // 在上次是0下次是1的时候j = i 然后i ++ 了但是j没有，所以差值仍然是+1

    // 如果连续几次都是nums[i] = 1 那么i一直在增加，j没有增加差值刚好是增加的次数。
        if nums[i] == 0 {
            j = i
        }else {
            if count < i - j {
                count = i -j 
            }

        }
    }
    return count
}

```

**通用动态规划的写法**

按照步骤分析，每一次要么是1 要么是0 而且按照步骤分析前后并没有影响的关系。
如果看不懂就算了可以去使用第一种解法。

```go

func findMaxConsecutiveOnes(nums []int) int {
    if len(nums) == 0 {
        return 0
    }
    res  := 0
    result := make([]int,len(nums))
    result[0] = nums[0]
    for i := 1; i < len(result);i++ {
        if nums[i] == 0 {
            result[i] = 0
        }else {
            result[i]= result[i-1]+1
        }
    }
    for _,x := range result {
        if x > res {
			res = x
		}
    }
    return res
}

```

## 有序矩阵中第K小的元素

> [leetcode](https://leetcode-cn.com/problems/kth-smallest-element-in-a-sorted-matrix/description/)

一道矩阵的题，我发现它的解体方法可以有多达十几种。
当然有一些是暴力法复杂度非常高，但是这种锻炼思维的题真是好题.

有序矩阵中第K小的元素

给定一个 n x n 矩阵，其中每行和每列元素均按升序排序，找到矩阵中第k小的元素。
请注意，它是排序后的第k小元素，而不是第k个元素。

示例:
```bash
matrix = [
[ 1,  5,  9],
[10, 11, 13],
[12, 13, 15]
],
k = 8,
```
返回 13。
说明:
你可以假设 k 的值永远是有效的, 1 ≤ k ≤ n2 。

--- 

 暴力求解 时间复杂度 nm + nmlognm(go默认大数据是快排); 空间复杂度是 nm
 ```go
func kthSmallest1(matrix [][]int, k int) int {
	if len(matrix) == 0 {
		return 0
	}

	l := make([]int, len(matrix)*len(matrix[0]))
	t := 0
	for i := 0; i < len(matrix); i++ {
		for j := 0; j < len(matrix[i]); j++ {
			l[t] = matrix[i][j]
			t++
		}
	}
	sort.Ints(l)
	return l[k-1]
}
```
暴力求解，使用堆进行处理
```go
func kthSmallest2(matrix [][]int, k int) int {
	he := new(bds.IntHeap) // 初始化一个堆
	for i := 0; i < len(matrix); i++ {
		for j := 0; j < len(matrix[i]); j++ {
			heap.Push(he, matrix[i][j]) // 一股脑将所有数据传入这个堆中
		}
	}
	var result interface{}
	for i := 0; i < k; i++ {
		result = heap.Pop(he) //
	}
	return result.(int)

}
```
 


可以看到这种写法效率不高，并且还有额外的空间复杂度，尤其是它的时间复杂度基本上是n^2+klogn (n^2是遍历数据,klogn是堆化)

使用堆进行求解，但是维护一个length为k的堆即可。
```go
func kthSmallest3(matrix [][]int, k int) int {
	he := new(bds.IntHeapM) // 就是实现了一个大顶堆而已。
	for i := 0; i < len(matrix); i++ {
		for j := 0; j < len(matrix[i]); j++ {
			// 当数据小于等于7的时候一直增加

			if he.Len() < k {
				heap.Push(he,matrix[i][j])
			} else { // 否则进行对比，然后开始排出数据。
				if he.Top().(int) > matrix[i][j] { // 如果 top（最小值） 都比 某个值大 那么
					heap.Pop(he)
					heap.Push(he,matrix[i][j])
				}
			}
		}
	}
	fmt.Println(he)
	return he.Top().(int)
}
```
 多路归并的求解方法(取各路的最小值进行 min(min1,min2,min3,min4))

 ```go
func kthSmallest4(matrix [][]int, k int) int {
    if len(matrix) ==0 {
        return 0
    }

	heapSpec := new(BDS.IntHeapSpec) // github.com/googege/BDS

	//init 初始化这个小顶堆。
	for i := 0; i < len(matrix); i++ {

		l := [3]int{
			i, 0, matrix[i][0],
		}

		heapSpec.Push(l)

	}
	// 使用对路归并原理（min(min1,min2,min3)）
	for i := 1; i <= k-1; i++ {

		result := heap.Pop(heapSpec).([3]int)
		if result[0] < len(matrix) && result[1] < len(matrix[0])-1 {
			heap.Push(heapSpec, [3]int{result[0], result[1] + 1, matrix[result[0]][result[1]+1]})
		}

	}
	return heapSpec.Top().([3]int)[2]
}

```
 二分法求解
  二分法可以取索引，也可以直接使用数字。比如这道题用的就是
二分法的数字，l和r直接用mid+1 -1 因为这个题是整型，所以这么干OK的。

 ```go
func kthSmallest5(matrix [][]int, k int) int {
	if len(matrix) <=0 {
		return 0
	}

	m, n := len(matrix), len(matrix[0])
	l, r := matrix[0][0], matrix[m-1][n-1]

	for l <= r {

		mid := l + (r-l)>>1// 此处是为了防止out of index
		nt := 0 // 为了测试是第k小的左边还是右边。

		for i := 0; i < len(matrix); i++ {
			for j := 0; j < len(matrix[0]) && matrix[i][j] <= mid; j++ {
				nt++
			}
		}
// 如果 matrix[i][j] 小于mid但是退出了，那么l就等于mid-1 然后刚好是我们要得到的数据。如果等于，也刚好还是l 如果小于那么就改r，
		if nt < k {
			l = mid + 1
		} else {
			r = mid - 1
		}

	}

	return l
}
```

BFPRT 算法，也就是所谓的快排的变种，快速选择

> 解决topk问题，有两个通解 1 堆 2 快选 堆的话就是维护一个k大小的最小最大堆即可，快选就是利用快排的原理将要的part排序不需要的不排序就OK，然后还有一个通解就是多路归并，通常来说这个算法是解决外部排序的时候使用的，先将数据分成一个一个的小块，然后对这些数据进行min（min1，min2...min n）这个算法的原理来自于归并排序

> 总结一下：基础10种排序算法很重要，归并 -> 多路归并 ，快排 -> BFPRT快速选择，堆排序 -> 解决top问题的堆 ，by the way，堆在多路归并中是作为基础数据结构来用的，所以掌握好基础数据结构和基础算法非常重要！
//todo  快排的时候再搞定。
```go
func kthSmallest6(matrix [][]int, k int) int {

}
```


## 错误的集合

> [leetcode](https://leetcode-cn.com/problems/set-mismatch/description/)

> 鸽巢理论

集合 S 包含从1到 n 的整数。不幸的是，因为数据错误，导致集合里面某一个元素复制了成了集合里面的另外一个元素的值，导致集合丢失了一个整数并且有一个元素重复。

给定一个数组 nums 代表了集合 S 发生错误后的结果。你的任务是首先寻找到重复出现的整数，再找到丢失的整数，将它们以数组的形式返回。

示例 1:
```bash
输入: nums = [1,2,2,4]
输出: [2,3]
注意:
```
给定数组的长度范围是 [2, 10000]。
给定的数组是无序的。


解法就是鸽巢理论，代表的数字到自己的索引值去（本题是数字-1对应索引因为没有0）
然后**那些不该在自己巢里的就是重复的，这个巢本身该有的但是没有的就是缺失的**。这就是鸽巢理论

```go
func findErrorNums(nums []int) []int {
    if len(nums) == 0 {
        return nil
    } 

    result := make([]int,0)
    for i := 0; i < len(nums);i++ {
        for i != nums[i] -1 && nums[nums[i] - 1] != nums[i] {
             这里的for是为了避免比如已经在自己的索引值上的，
             然后第二个是自己的索引值已经有了自己一样的数字的
             然后也不用再交换了。
            swap(nums,i,nums[i]-1)
        }
    }
    for i := range nums {
        if nums[i] - 1 != i {
            result = append(result,nums[i],i+1)
        }
     }
     return result
}

func swap(nums []int,a,b int){
    nums[a],nums[b] = nums[b],nums[a]
}

```
---
# 找到所有数组中消失的数字
> [leetcode](https://leetcode-cn.com/problems/find-all-numbers-disappeared-in-an-array/description/)

> 鸽巢理论

给定一个范围在  1 ≤ a[i] ≤ n ( n = 数组大小 ) 的 整型数组，数组中的元素一些出现了两次，另一些只出现一次。

找到所有在 [1, n] 范围之间没有出现在数组中的数字。

您能在不使用额外空间且时间复杂度为O(n)的情况下完成这个任务吗? 你可以假定返回的数组不算在额外空间内。

示例:
```bash
输入:
[4,3,2,7,8,2,3,1]

输出:
[5,6]
```
这道题也是典型的鸽巢理论，而且跟上一次基本上一摸一样，只是不需要把重复的数字给输出即可
```go
func findDisappearedNumbers(nums []int) []int {
    if len(nums) == 0 {
        return nil
    }
    result := make([]int,0)
    for i := 0;i< len(nums);i++ {
        for nums[i]-1 != i && nums[i] != nums[nums[i]-1] {
            swap(nums,nums[i]-1,i)
        }
    }
    for k := range nums {
        if nums[k]-1 != k  {
                result = append(result,k+1)  将这个巢穴代表的数字输出即可（代表的是大于一的数字）
        }
    }
    return result
}

func swap(a []int,b,c int){
    a[b],a[c] = a[c],a[b]
}
```
---

#  数组中重复的数据
> [leetcode](https://leetcode-cn.com/problems/find-all-duplicates-in-an-array/description/)

> 鸽巢理论

给定一个整数数组 a，其中1 ≤ a[i] ≤ n （n为数组长度）, 其中有些元素出现两次而其他元素出现一次。

找到所有出现两次的元素。

你可以不用到任何额外空间并在O(n)时间复杂度内解决这个问题吗？

示例：
```bash
输入:
[4,3,2,7,8,2,3,1]

输出:
[2,3]
```

```go

func findDisappearedNumbers(nums []int) []int {
    if len(nums) == 0 {
        return nil
    }
    result := make([]int,0)
    for i := 0;i< len(nums);i++ {
        for nums[i]-1 != i && nums[i] != nums[nums[i]-1] {
            swap(nums,nums[i]-1,i)
        }
    }
    for k := range nums {
        if nums[k]-1 != k  {
                result = append(result,nums[k]) // 将这里的在别人家的鸽子输出即可。
        }
    }
    return result
}

func swap(a []int,b,c int){
    a[b],a[c] = a[c],a[b]
}

```
---

## 找出数组中重复的数，数组值在 [1, n] 之间
 > [leetcode](https://leetcode-cn.com/problems/find-the-duplicate-number/description/)
 ### 解法一 双指针 [快慢指针这里有几个释例](https://blog.csdn.net/qq_21815981/article/details/79833976)

 这道题跟上面几道很类似，只是限制更多，其实如果不在意空间，用哈希是最快的方法，也是最容易写的。速度也OK。

```go
func findDuplicate(nums []int) int { // 这种写法是标准的链表双指针寻找环的写法。
    low,fast := nums[0],nums[nums[0]]// 首先制定固有的速率，low是nums[low]的速度，fast是二倍于它的速度就是 nums[nums[0]]
    for low != fast {
        low = nums[low] // 按照不同的速度（存在倍数关系），如果这两者能相遇，就只有一个原因就是因为存在环。
        fast = nums[nums[fast]]
    }
    // 但是他们相遇的地方不一定是入口处，有可能是环的内部的某个元素，这个元素叫做相遇点。
    low = 0// 然后这一步我门要寻找这个环的入口，将low设置为0就是从头开始走，然后fast在相遇点。这个时候把他们的速度调成一样。
    // 即： low = nums[low] fast = nums[fast] 这样速度就一样了，（都是一次，之前速度不同的时候fast直接两次）
    for low != fast {
        low = nums[low]
        fast = nums[fast]
    }
    return low
}
```
 ### 解法二 二分法
```go
/ 这道题的关键是 数字都在1-n之间。
// 如果是中位数是4 那么在4左边的数字最多是4，如果多了就是重复数字了，这就是这个题的解法
func findDuplicate(nums []int) int {
    left := 1 //题目中给定的数字是1-n 所以最小值是1 最大的是n 就是 len-1
    right := len(nums)-1
    for left <= right { // 当左小于等于右的时候方才可以急需遍历。
        mid := left +(right-left)>>1 // 求数字的中位数（这里不是求的索引）
        nn := 0 // 设置一个计时器
        for i := 0;i < len(nums);i++ {
            if nums[i] <= mid{ // 计算middle左边的数字的数量
                    nn++ 
            }
           
        }
        if nn >mid { // 如果这个数字比middle小了，那证明左边有重复的，right就要去更改了
            right  = mid-1
        }else { // 如果小了，那么右边肯定是重复了，那么left就要更改了。
            left= mid +1
        }
    }
return left // 最后输出left即可。这个left就是要求的值。

}
```

 ## 数组相邻差值的个数
> [leetcode](https://leetcode-cn.com/problems/beautiful-arrangement-ii/description/)

这是一道找规律的题：//下标从[0, k]中，偶数下标填充[1,2,3…]，奇数下标填充[k + 1, k, k - 1…]，后面[k + 1, n - 1]都是顺序填充
```go
func constructArray(n int, k int) []int {
    让 := make([]int,0,n)
    
    i,j := 1,n
    for i <= j {
        if k %2 ==1 {
            让 = append(让,i)
            i++
        }else {
            让 = append(让,j)
            j--
        }
        if k >1 {
           k--
        }
    }
}
```


## 数组的度
> [leetcode](https://leetcode-cn.com/problems/degree-of-an-array/description/)

```go

```

## 对角元素相等的矩阵
> [leetcode](https://leetcode-cn.com/problems/toeplitz-matrix/description/)

```go
```

## 嵌套数组
> [leetcode](https://leetcode-cn.com/problems/array-nesting/description/)

```go
```
## 分隔数组
> [leetcode](https://leetcode-cn.com/problems/max-chunks-to-make-sorted/description/)

```go
```


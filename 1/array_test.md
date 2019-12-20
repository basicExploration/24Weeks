# array 测试

## 测试ArrFindDupLicateNumbers
```go
func TestArrFindDuplicateNumbers(t *testing.T) {
	// 测试事例。
	// 错误事例
	d := make([]int, 10)
	arr1 := []int{1, 12}
	// 一般事例
	arr2 := []int{1, 4, 5, 4, 5, 3, 2}
	// 边缘事例
	arr3 := []int{1, 1, 1, 1}
	// 边缘事例
	arr4 := []int{3, 4, 5, 2, 1, 0}
	ArrayFindDuplicateNumbers(arr1, d)
	ArrayFindDuplicateNumbers(arr2, d)
	ArrayFindDuplicateNumbers(arr3, d)
	ArrayFindDuplicateNumbers(arr4, d)

}
```


---
title: 一种低效的快速排序方法
thumbnail: 'https://cdn.sguan.top/markdown/20200910131930.png'
disqusId: edb08151-54df-4399-89a7-4a0d68702fe7
categories:
  - sort
tags:
  - sort
date: 2020-09-10 13:18:37
---

快速排序是一种高效的排序方法，其拥有平均 `O(n*log(n))` 的时间复杂度，并且往往比其它相同复杂度的排序算法快得多。

<!-- more -->

## 算法思想

快速排序的基本思想是：

1．先从数列中取出一个数作为基准数。
2．分区过程，将比这个数大的数全放到它的右边，小于或等于它的数全放到它的左边。
3．再对左右区间重复第二步，直到各区间只有一个数。

## 实现

我在网上找到了很多实现，其实现大概是这样：

```go
func Sort(a []int, l, h int) {
	if l < h {
		m := partition(a, l, h)
		Sort(a, l, m)
		Sort(a, m+1, h)
	}
}

func partition(source []int, l, h int) int {
	pivot := source[l]
	left, right := l, l+1
	for ; right < h; right++ {
		if source[right] <= pivot {
			left++
			source[left], source[right] = source[right], source[left]
		}
	}
	source[l], source[left] = source[left], source[l]
	return left
}
```

根据 go slice 的特性，我将其改写为了不需要传入额外参数的实现：

```go
func QuickSort1(arr []int) {
	if len(arr) < 2 {
		return
	}
	pivot := arr[0]
	left, right := 0, 1
	for ; right < len(arr); right++ {
		if arr[right] <= pivot {
			left++
			arr[left], arr[right] = arr[right], arr[left]
		}
	}
	arr[0], arr[left] = arr[left], arr[0]
	QuickSort1(arr[:left])
	QuickSort1(arr[left+1:])
}
```

对于长度为 100'000 的数组，其基准测试速度是选择排序法的数十~数百倍。但是还有更高效的快排实现。

## 高效的快排

我在网上找到了另外一种实现：

```go
func Sort(arr []int, start, end int) {
	if start < end {
		left, right := start, end
		pivot := arr[(start+end)/2]
		for left <= right {
			for arr[left] < pivot {
				left++
			}
			for arr[right] > pivot {
				right--
			}
			if left <= right {
				arr[left], arr[right] = arr[right], arr[left]
				left++
				right--
			}
		}

		if start < right {
			Sort(arr, start, right)
		}
		if end > left {
			Sort(arr, left, end)
		}
	}
}
```

根据 go slice 的特性，我将其改写为了不需要传入额外参数的实现：

```go
func QuickSort2(arr []int) {
	if len(arr) < 2 {
		return
	}
	start, end := 0, len(arr)-1
	left, right := start, end
	pivot := arr[(start+end)/2]
	for left <= right {
		for arr[left] < pivot {
			left++
		}
		for arr[right] > pivot {
			right--
		}
		if left <= right {
			arr[left], arr[right] = arr[right], arr[left]
			left++
			right--
		}
	}
	if start < right {
		QuickSort2(arr[:right+1])
	}
	if end > left {
		QuickSort2(arr[left:])
	}
}
```

这种实现方式，看起来时间复杂度会比较高，算法实现也比较复杂，但在基准测试中，其效率比前一种方式更快。

## 基准测试

```bash
$ go test -bench=.  -benchmem -benchtime=3s -run=none
goos: windows
goarch: amd64
BenchmarkSelectionSort-12              2        2501092500 ns/op               8 B/op          1 allocs/op
BenchmarkBubbleSort-12                 1        3992059100 ns/op               0 B/op          0 allocs/op
BenchmarkQuickSort1-12               118          31965469 ns/op               0 B/op          0 allocs/op
BenchmarkQuickSort2-12              3511           1072784 ns/op               0 B/op          0 allocs/op
BenchmarkSortSort-12                1129           3104246 ns/op              64 B/op          2 allocs/op
PASS
ok      _/D_/codes/gomod/Leetcode/basic/sort-1  30.682s

```

slice 长度为 100'000

四种排序方法分别是：选择排序、第一种快排、高效的快排、系统库的排序实现

可以看到第一种快排速度较慢，与高效的快排方式大概相差十倍。

高效的快排与系统库的实现由于 interface，大概有 `3-4倍` 的差距，但能接受。 

## 小结

网上充斥着大量低效的快排教程，在基准测试中就可以看出来，两者存在着数百倍的差距。

一些高效快排代码，其实也没有将代码的优化思想讲清楚，可能是我没有理解到位吧，先 mark 住，以后有空再看看。
 
## 参考链接

[快速排序和归并排序(golang实现)](https://www.jianshu.com/p/458fbbe39f59)

[golang实现改进版的快速排序](https://studygolang.com/articles/3413)

[白话经典算法系列之六 快速排序 快速搞定](https://blog.csdn.net/MoreWindows/article/details/6684558)


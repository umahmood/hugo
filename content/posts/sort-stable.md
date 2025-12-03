+++
date = "2016-03-13T19:34:13Z"
draft = false
title = "sort.Sort & sort.Stable"
hideToc = true

+++

Go 1.6 made improvements to the Sort function in the sort package. It was improved to make fewer calls to the Less and Swap methods. Here are some benchmarks showing the performance of sort.Sort in Go 1.5 vs 1.6:

```
Sort []int with Go 1.5
BenchmarkSort_1-4       20000000              67.2 ns/op
BenchmarkSort_10-4      10000000               227 ns/op
BenchmarkSort_100-4       500000              3863 ns/op
BenchmarkSort_1000-4       30000             52189 ns/op

Sort []int with Go 1.6
BenchmarkSort_1-4       20000000              64.7 ns/op
BenchmarkSort_10-4      10000000               137 ns/op
BenchmarkSort_100-4       500000              2849 ns/op
BenchmarkSort_1000-4       30000             46949 ns/op
```

_source: [state of go](https://talks.golang.org/2016/state-of-go.slide#24)_

Sort does not use a stable sorting algorithm, it does not make any guarantees about the final order of equal values. A stable sort algorithm, is one in which items which have the same key stay in the same relative order during the sort. The sorting algorithms mergesort and radixsort are stable, were as quicksort, heapsort and shellsort are not stable. If this property is important to your application then you may want to use sort.Stable.

[sort.Sort](https://golang.org/src/sort/sort.go?s=5443:5468#L211) under the hood uses the quicksort algorithm, were as [sort.Stable](https://golang.org/src/sort/sort.go?s=10143:10170#L336) uses insertion sort. Below is an example of Sort and Stable in action:

```
type byLength []string

func (b byLength) Len() int           { return len(b) }
func (b byLength) Less(i, j int) bool { return len(b[i]) < len(b[j]) }
func (b byLength) Swap(i, j int)      { b[i], b[j] = b[j], b[i] }

func main() {
    values1 := []string{"ball", "hell", "one", "joke", "fool", "moon", "two"}
    sort.Sort(byLength(values1))
    fmt.Println("sort.Sort", values1)

    values2 := []string{"ball", "hell", "one", "joke", "fool", "moon", "two"}
    sort.Stable(byLength(values2))
    fmt.Println("sort.Stable", values2)
}
```

Output:

```
sort.Sort   [two one hell joke fool moon ball]
sort.Stable [one two ball hell joke fool moon]
```

Fin.

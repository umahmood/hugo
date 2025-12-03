+++
date = "2016-10-22T21:45:22+01:00"
draft = false
title = "Using Subtests and Sub-benchmarks in Go"
hideToc = true

+++

In this post we will walk through an example of how to use the new subtests and sub-benchmarks functionality introduced in Go 1.7.

#### Subtests

One of the nifty features in Go is the ability to write table driven tests. For example, if we wanted to test the function:

```go
func Double(n int) int {
    return n * 2
}
```

Then we could write a table driven test as follows:

```go
func TestDouble(t *testing.T) {
	testCases := []struct {
		n    int
		want int
	}{
		{2, 4},
		{4, 10},
		{3, 6},
	}
	for _, tc := range testCases {
		got := Double(tc.n)
		if got != tc.want {
			t.Errorf("fail got %v want %v", got, tc.want)
		}
	}
}
```

**Note:** The test case {4, 10} is present to make the test fail, 4 \* 2 != 10 😃.

If we run this test, we get the following output:

```
$ go test -v
=== RUN   TestDouble
--- FAIL: TestDouble (0.00s)
        example_test.go:25: fail got 8 want 10
FAIL
exit status 1
FAIL    example    0.005s
```

The problem here is that we don't know which table test case failed. It would be better, if we could identify a table test case, and display its name in the output if it fails.

This is what subtests in GO 1.7 allow us to do. The _testing.T_ type now has a _Run_ method, were the first argument is a string (the name of the test). And the second argument is a function. Below we re-implement the above test, using the _Run_ method:

```go
func TestDouble(t *testing.T) {
	testCases := []struct {
		n    int
		want int
	}{
		{2, 4},
		{4, 10},
		{3, 6},
	}
	for _, tc := range testCases {
		t.Run(fmt.Sprintf("input_%d", tc.n), func(t *testing.T) {
			got := Double(tc.n)
			if got != tc.want {
				t.Errorf("fail got %v want %v", got, tc.want)
			}
		})
	}
}
```

A few things to note here are that one, we are setting the name of the test to the 'n' value of the test case. So our tests are named "input_2", "input_3" and "input_4". And two, for the second parameter we are passing in a closure which has the same method signature as a normal test.

If we run this test, we get the following output:

```
$ go test -v
=== RUN   TestDouble
=== RUN   TestDouble/input_2
=== RUN   TestDouble/input_3
=== RUN   TestDouble/input_4
--- FAIL: TestDouble (0.00s)
    --- PASS: TestDouble/input_2 (0.00s)
    --- PASS: TestDouble/input_3 (0.00s)
    --- FAIL: TestDouble/input_4 (0.00s)
        example_test.go:43: fail got 8 want 10
FAIL
exit status 1
FAIL    example    0.006s
```

This time we get a more detailed output, we can see that "input_4" was the failing test case from the table. And the pass/fail status of each individual table test case.

We can run a subset of our table tests, by matching the unique names set for them (the first parameter to the Run method), as follows:

```
$ go test -v -run="TestZap/input_2"
=== RUN   TestZap
=== RUN   TestZap/input_2
--- PASS: TestZap (0.00s)
    --- PASS: TestZap/input_2 (0.00s)
PASS
ok      example    0.008s
```

Running many tests, by matching the test names:

```
$ go test -v -run="TestZap/input_[1-3]"
=== RUN   TestZap
=== RUN   TestZap/input_2
=== RUN   TestZap/input_3
--- PASS: TestZap (0.00s)
    --- PASS: TestZap/input_2 (0.00s)
    --- PASS: TestZap/input_3 (0.00s)
PASS
ok      example    0.006s
```

Here _"input[1-3]"_ matched _"input_2"_ and _"input_3"_ but not _"input_4"_.

#### Sub-benchmarks

Unlike table driven testing there was no equal approach for benchmarking. But now in Go 1.7, we have the ability to create table driven benchmarks. Imagine we need to benchmark the following function:

```go
func AppendStringN(s string, n int) {
	a := make([]string, 0)
	for i := 0; i < n; i++ {
		a = append(a, s)
	}
}
```

We can define a top-level benchmark function like this:

```go
func BenchmarkAppendStringN(b *testing.B) {
	benchmarks := []struct {
		fruit string
		n     int
	}{
		{fruit: "apple", n: 10},
		{fruit: "pear", n: 20},
		{fruit: "mango", n: 40},
		{fruit: "berry", n: 60},
		{fruit: "banana", n: 80},
		{fruit: "orange", n: 100},
	}
	for _, bm := range benchmarks {
		b.Run(bm.fruit, func(b *testing.B) {
			for i := 0; i < b.N; i++ {
				AppendStringN(bm.fruit, bm.n)
			}
		})
	}
}
```

The _Run_ methods signature is the same as described above. But for the _testing.B_ type, rather than the _testing.T_ type. Our benchmark names are set to "apple", "pear", "mango", etc...

If we run this benchmark, we get the following output:

```
$ go test -v -run="xxx" -bench=.
BenchmarkAppendStringN/apple-8           3000000               495 ns/op
BenchmarkAppendStringN/pear-8            2000000               713 ns/op
BenchmarkAppendStringN/mango-8           1000000              1101 ns/op
BenchmarkAppendStringN/berry-8           1000000              1156 ns/op
BenchmarkAppendStringN/banana-8          1000000              1803 ns/op
BenchmarkAppendStringN/orange-8          1000000              1153 ns/op
PASS
ok      example_test    9.428s
```

An important thing to note is that, each time _b.Run_ is invoked it creates a separate benchmark. The outer benchmark function (BenchmarkAppendStringN) is only run once and it is not measured.

One last thing to mention is that, like subtests. You can run individual benchmarks by there set unique names. Below, we run just the "berry" benchmark:

```
$ go test -v -run="xxx" -bench="/berry"
BenchmarkAppendStringN/berry-8           1000000              1143 ns/op
PASS
ok      subtestbench    1.165s
```

I hope you have found this blog post helpful.

Fin.

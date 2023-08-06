# Usage

You can view current version this presentation [here](https://bit.ly/dotgo2019)

# License and Materials

This presentation is licensed under the [Creative Commons Attribution-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-sa/4.0/) licence.

You are encouraged to remix, transform, or build upon the material, providing you give appropriate credit and distribute your contributions under the same license.

# Cheat sheet

## Benchmarks

### Basic command

```sh
go test -bench=. -cpu=1,2,4 -run=^$ ./examples/fib/
```

### Comparison with benchstat

```sh
go test -c
mv fib.test fib.golden
go test -c
./fib.golden -test.bench=. -test.count=10 > old.txt
./fib.test -test.bench=. -test.count=10 > new.txt
benchstat old.txt new.txt
```

### Look at assembly

```sh
go test -gcflags=-S
# disable inlining
go test -gcflags="-l -S"
```

### Fixing compiler optimizations

```go
var Result uint64

func BenchmarkPopcnt(b *testing.B) {
	var r uint64
	for i := 0; i < b.N; i++ {
		r = popcnt(uint64(i))
	}
	Result = r
}
```

## Profiling

### Profile benchmark

```sh
go test -run=XXX -bench=. -cpuprofile=c.p bytes
go tool pprof c.p
```

### HTTP option (with flame graph mode)

```sh
go tool pprof -http=:8080 $FILENAME
```

## Optimizations

### Escape analysis & inlining

```sh
go build -gcflags=-m examples/esc/sum.go
go build -gcflags='-m -m' examples/esc/sum.go

go build -gcflags=-m examples/inl/max.go
```

## Execution tracer

### Get the trace

```go
import "github.com/pkg/profile"

func main() {
	defer profile.Start(profile.TraceProfile, profile.ProfilePath(".")).Stop()
    // ...
}
```

### Analyze the trace

```sh
go tool trace trace.out
```

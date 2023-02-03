
Blog by [Dave Cheney](https://dave.cheney.net/2020/04/25/inlining-optimisations-in-go)


Inlining might differ between go compilers we focuse on the [golang.org](https://github.com/golang/go)

What is inlining: 

>Inlining is the act of combining smaller functions into their respective callers. In the early days of computing this optimisation was typically performed by hand. Nowadays inlining is one of a class of fundamental optimisations performed automatically during the compilation process.
|> Dave cheney

You can inline yourself but this is focused on how the compler inline a function instead of refering to a function.  The code is where it is run. 


Inlining is important for two reasons:
- The first is it removes the overhead of the function call itself. 
- The second is it permits the compiler to more effectively apply other optimisation strategies.

> Calling a function[1](https://dave.cheney.net/2020/04/25/inlining-optimisations-in-go#easy-footnote-bottom-1-4053) in any language carries a cost.

This is the cost that inlining will reduce. 

Calling function -> data to resigeter or stack

Jumping program counter -> can give pipeline stall

Everytime we enter function we check stack space if not enogh we copy the stack to a larger location. 

**This is what GO wants:** 
When we enter we will know how much will end on the stack, for unkown types, variables they will end on the stack. That is the definition - all that is unkown end up on the heap!

**What the runtime does is:**
This check check is cheap so we CAN hide it. The check is rarley done on modern CPU:s instead we assume it is succesfull and if we had a miss prediction we discard the work (done speculativly).  This is a pipeline stall? We also have to rerun the work. The pipeline stall vs the cost of growing the stack is small, due to that we often dont need to grow the stack? 

Even with modern CPU and cool tricks there will be an overhead that adds to the acuall time. 

> As a function call’s overhead is fixed, smaller functions pay a larger cost relative to larger ones because they tend to do less useful work per invocation.
> 

To elimate the overhead we COULD elimite the function call.  This is inlining and is partly done auto magic by go. 

> _the_ optimisation performed by modern compilers as it forms the basis for optimisations like constant propagation and dead code elimination
> [Dr. Cliff Click](http://cliffc.org/blog/)


Using inlining a compiler is allowed to see more in to the code, further in to the context of the particular function.  Doing this recursivly we can apply this to a chain of functions. 

```go 
package main

import "testing"

//go:noinline
func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}

var Result int

func BenchmarkMax(b *testing.B) {
	var r int
	for i := 0; i < b.N; i++ {
		r = max(-1, i)
	}
	Result = r
}
```

Inlining makes it 78 % faster. Check the source for [more info](https://dave.cheney.net/2020/04/25/inlining-optimisations-in-go)

Speed up comes from 2 things: 
- No function overhead discussed above
- We remove dead code path due to that `-1 > r` will never be true

The inline function vs not having a function gives the same speed up. 

In the end we will end up with: 

```go 
func BenchmarkMax(b *testing.B) {
    var r int
    for i := 0; i < b.N; i++ {
        r = i
    }
    Result = r
}
```

> Inlining helps us write effective code that is easy to read.

Today we discussed leaf inlining, we will follow up with inline in the middle. 


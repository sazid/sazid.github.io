---
---
Suddenly I had the urge to learn the basics of Go (lang) and see
what's so special about it. So, I went to https://golang.org/ and from
there I started the "A Tour of Go" short tutorial. It's kind of like
an interactive tutorial. As I progressed through the tutorial, I found
a very interesting algorithm for finding the square root of a
number. It's the **Newton-Raphson root-finding algorithm**. I
literally forgot about it, but I learnt about this in my 5th
semester's math course.
https://en.wikipedia.org/wiki/Newton%27s_method

I should quote one important part from the tour:

> If you are interested in the details of the algorithm, the z² − x
above is how far away z² is from where it needs to be (x), and the
division by 2z is the derivative of z², to scale how much we adjust z
by how quickly z² is changing. This general approach is called
Newton's method. It works well for many functions but especially well
for square root.

## Code

```go
package main

import (
    "fmt"
    "math"
)

func Sqrt(x float64) float64 {
    z := 1.0

    for i := 1; i <= 15; i++ {
        z -= (z*z - x) / (2*z)
    }

    return z
}

func main() {
    for i := 1; i <= 20; i++ {
		fmt.Println(i, math.Sqrt(float64(i)), Sqrt(float64(i)))
    }
}
```

Output:

```
1 1 1
2 1.4142135623730951 1.4142135623730951
3 1.7320508075688772 1.7320508075688772
4 2 2
5 2.23606797749979 2.23606797749979
6 2.449489742783178 2.449489742783178
7 2.6457513110645907 2.6457513110645907
8 2.8284271247461903 2.82842712474619
9 3 3
10 3.1622776601683795 3.162277660168379
11 3.3166247903554 3.3166247903554
12 3.4641016151377544 3.4641016151377544
13 3.605551275463989 3.605551275463989
14 3.7416573867739413 3.7416573867739413
15 3.872983346207417 3.872983346207417
16 4 4
17 4.123105625617661 4.123105625617661
18 4.242640687119285 4.242640687119286
19 4.358898943540674 4.358898943540673
20 4.47213595499958 4.47213595499958
```

**Note:** the left most numbers indicate line numbers, not output
values. First column indicates **i**, second column indicates standard
library's **math.Sqrt()** function and the third column indicates my
hand-written **Sqrt()** function using the Newton-Raphson root finding
algorithm.

As seen from the output results, the algorithm is quite accurate even
for just **10-15 iterations** it gives us almost the same accuracy as
the standard library's square root function. I think, I might start
using it for my own code instead of the C/C++ builtin sqrt() function.

Sourcedata
===

Sourcedata helps you to generate source files containing values computed from python. It can be used for testing code in other languages against python implementations.

### C++ Demo

Suppose `logsumexp_testgen.py` contains the following:

```python
import math
import sourcedata

@sourcedata.generator(namespace="Testdata")
def logsumexp_testdata():
	inputs = [1., 2., 3., 4., 5.]
	yield "inputs", inputs
	expected = math.log(sum(map(math.exp, inputs)))
	yield "expected", expected
```

The running

```bash
$ sourcedata logsumexp_testgen.py -o testdata.h --namespace Testdata
```

will generate a file named `testdata.h` containing:

```c++
// logsumexp_testdata
namespace Testdata {
    double inputs[5] = {1.0, 2.0, 3.0, 4.0, 5.0};
    double expected = 5.451914395937593;
}
```

You might then use this to test some c++ code:

```c++
#include <cmath>
#include <vector>
#include <cassert>

#include "testdata.h"

double logsumexp(const std::vector<double>& xs) {
	double sum = 0.;
	for (double x : xs) {
		sum += std::exp(x);
	}
	return std::log(sum);
}

int main(int argc, char ** argv) {
	std::vector<double> xs(Testdata::inputs, Testdata::inputs+5);
	assert(logsumexp(xs) == Testdata::expected);
}
```

### Golang Demo

Suppose `logsumexp_testgen.py` contains the following:

```python
import math
import sourcedata

@sourcedata.generator()
def logsumexp_testdata():
	inputs = [1., 2., 3., 4., 5.]
	yield "inputs", inputs
	expected = math.log(sum(map(math.exp, inputs)))
	yield "expected", expected
```

And suppose `logsumexp_test.go` contains the following:

```golang
//go:generate sourcedata logsumexp_testgen.py -o logsumexp_testdata.go --package main

package main

import (
	"math"
	"testing"
	"github.com/stretchr/testify/assert"
)

func LogSumExp(xs []float64) float64 {
	sum := 0.
	for _, x := range xs {
		sum += math.Exp(x)
	}
	return math.Log(sum)
}

func TestLogSumExp(t *testing.T) {
	assert.Equal(t, expected, LogSumExp(inputs))
}
```

Then we can run

```bash
$ go generate
$ go test
PASS
ok  	_/Users/alex/Code/test_sourcedata/golang	0.005s
```

The call to `go generate` will have generated the following file:

```golang
package main

// logsumexp_testdata
var inputs = []float64{1.000000000000, 2.000000000000, 3.000000000000, 4.000000000000, 5.000000000000}
var expected float64 = 5.451914395937593
```

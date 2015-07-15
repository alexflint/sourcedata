Sourcedata
===

Sourcedata helps you to generate source files containing values computed from python. It can be used for testing code in other languages against python implementations:

```python
import math
import sourcedata

@sourcedata.generator(namespace="Testdata")
def log_testdata():
	inputs = [1., 2., 3., 4., 5.]
	yield "inputs", inputs
	expected = math.log(sum(map(math.exp, inputs)))
	yield "expected", expected
```

```bash
$ sourcedata -o testdata.h --namespace Testdata
```

This will generate `testdata.h` containing:

```c++
// log_testdata
namespace Testdata {
    double inputs[5] = {1.000000000000, 2.000000000000, 3.000000000000, 4.000000000000, 5.000000000000};
    double expected = 5.451914395937593;
}
```

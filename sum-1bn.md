
#### TL;DR


Adding 1 billion numbers:

Language | Time (sec)
---------|----- 
R        | 1.3
py       | 1.0
C        | 0.7
java     | 1.2

This is all about 1 sec, and it is the single-thread memory bandwidth of the machine,
~8GB/sec (the numbers are 64-bit/8-byte doubles, i.e. 8GB in RAM).

A *simple* [parallelized version in C](https://gist.github.com/szilard/d3cb76839fddc2d529f3) 
runs in 0.5 sec on 16 cores (vs single-threaded 0.7 sec),
by exploiting that the multi-thread memory bandwidth is slightly larger than the single-threaded one
(but not proportionally with the number of threads/cores).
You can measure memory bandwith with [pmbw](https://panthema.net/2013/pmbw/).

It's certainly possible to beat that, and so you can for example with H2O, though if you use
H2O you should also consider that data is compressed in RAM, so on one hand it's faster to read
it over the bus, on the other hand it takes extra CPU cycles to decompress it. To have an apples-to-apples 
comparison, you could test on random doubles (instead of vector of ones), because in that 
case compression is minimal (I've got 0.4 sec 
[calling it from R/Python](https://gist.github.com/szilard/f9d3387c47c5d79aa9ab), 
but I'm sure that has overhead and one should time the sum from Java).


---------------------------------------------


#### R

```
x <- rep(1,1e9)
system.time({ s <- sum(x) })

#   user  system elapsed 
#  1.278   0.000   1.279 
```



#### python

```
import numpy as np
x = np.ones(1e9)
%time s = np.sum(x)

# CPU times: user 1.04 s, sys: 126 µs, total: 1.04 s
# Wall time: 1.04 s
```



#### C

```
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define N 1000000000
double x[N];

int main(int argc, char **argv)
{ 
  clock_t start, end;
  int i;
  double s;

  for(i=0; i<N; i++)
     x[i] = 1;

  start = clock();
  s = 0;
  for(i=0; i<N; i++)
     s += x[i];
  end = clock();

  printf("time: %f\n", (end - start)/1000000.);
  printf("sum: %e\n", s);
  return 0;
}
```

```
# gcc a.c && ./a.out 
# time: 3.698912
# sum: 1.000000e+09

# gcc -O3 a.c && ./a.out 
# time: 1.193752
# sum: 1.000000e+09

# gcc -Ofast a.c && ./a.out 
# time: 0.690078
# sum: 1.000000e+09
```



#### Java

```
public class Sum {

    public static void main (String[] args) {
  
      double[] x = new double[1000000000];
      double start, end;

      for (int i=0; i<x.length; i++) {
        x[i] = 1;
      }

      start = System.nanoTime();
      double s = 0;
      for (int i=0; i<x.length; i++) {
        s += x[i];
      }
      end = System.nanoTime();

      System.out.println ((end - start)/1e9);
      System.out.println (s);
    }
}
```

```
# javac Sum.java && java Sum
# 1.193732184
# 1.0E9
```

---------------------------------------------

#### HW+SW versions

```
model name      : Intel(R) Xeon(R) CPU           E5540  @ 2.53GHz
Data Width: 64 bits     Form Factor: DIMM      Speed: 1333 MHz

Ubuntu 14.04.3 LTS
R version 3.2.2 
print numpy.__version__   1.9.1
gcc (Ubuntu 4.8.4-2ubuntu1~14.04) 4.8.4
java version "1.8.0_60"
```



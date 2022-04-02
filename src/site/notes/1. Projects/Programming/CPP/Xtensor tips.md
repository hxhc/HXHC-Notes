---
{"dg-publish":true,"permalink":"/1-projects/programming/cpp/xtensor-tips/"}
---
## Installation

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">



</div>


Xtensor 系列主要包含 `xtl`, `xsimd`, `xtensor-blas`，`xtensor-fftw` 和 `xtensor`

## 1. Install `xtl`
`sudo apt-get install xtl-dev`

## 2. Install `xsimd`
- git clone
- set flags in CMakeList. txt
    - `OPTION (ENABLE_FALLBACK "build tests/benchmarks with fallback implementation" ON)` 
    - `OPTION (ENABLE_XTL_COMPLEX "enables support for xcomplex defined in xtl" ON)`
    - `OPTION(BUILD_EXAMPLES "xsimd examples" ON)`
    - 其他几项不要设置为 On，否则可能编译出错
- `mkdir build; cd build`
- `cmake ..`
- `make install`

在 `build` 下有一个 benchmark，名为`mandelbrot`，其结果如下，可以发现采用`AVX2`指令时，速度最快
```shell
$ ./mandelbrot 
starting benchmarks (results in 'ms')... 

scalar Statistics:
        max: 118
        min: 116
        median: 117
        median abs dev: 0
        mean: 116
        std dev: 0
        # of samples: 64

omp Statistics:
        max: 119
        min: 116
        median: 117
        median abs dev: 0
        mean: 116
        std dev: 1
        # of samples: 64

fma3+avx2 Statistics:
        max: 17
        min: 17
        median: 17
        median abs dev: 0
        mean: 17
        std dev: 0
        # of samples: 64

avx2 Statistics:
        max: 17
        min: 16
        median: 17
        median abs dev: 0
        mean: 16
        std dev: 0
        # of samples: 64

fma3+avx Statistics:
        max: 18
        min: 17
        median: 17
        median abs dev: 0
        mean: 17
        std dev: 0
        # of samples: 64

avx Statistics:
        max: 18
        min: 17
        median: 17
        median abs dev: 0
        mean: 17
        std dev: 0
        # of samples: 64

sse4.2 Statistics:
        max: 32
        min: 31
        median: 31
        median abs dev: 0
        mean: 31
        std dev: 0
        # of samples: 64

sse4.1 Statistics:
        max: 32
        min: 31
        median: 31
        median abs dev: 0
        mean: 31
        std dev: 0
        # of samples: 64

ssse3 Statistics:
        max: 32
        min: 32
        median: 32
        median abs dev: 0
        mean: 32
        std dev: 0
        # of samples: 64

sse3 Statistics:
        max: 32
        min: 32
        median: 32
        median abs dev: 0
        mean: 32
        std dev: 0
        # of samples: 64

sse2 Statistics:
        max: 32
        min: 32
        median: 32
        median abs dev: 0
        mean: 32
        std dev: 0
        # of samples: 64
```

## 3. 下载头文件
`Xtensor`, `Xtensor-blas` 和 `xtensor-fftw` 以头文件形式进行使用
[[1. Projects/Programming/CPP/Xtensor tips#Performance test|例子]]



</div></div>


## Compile
```shell
 g++ xtensor_performance.cpp -o ./output/xtensor_performance -m64 -I ./libs  -I"${MKLROOT}/include"  -L${MKLROOT}/lib -Wl,-rpath=${MKLROOT}/lib -lmkl_intel_lp64 -lmkl_gnu_thread -lmkl_core -lgomp -lpthread -lm -ldl  -O3
```
where ${MKLROOT} is referred to the installation location. See [[1. Projects/Programming/CPP/eigen tips#2 Install Intel OneMKL using conda|eigen tips]] for details.

## Performance test
### Matrix multiplication
```cpp
#define XTENSOR_USE_XSIMD
#define XTENSOR_USE_OPENMP

#include <ctime>
#include <chrono>
#include <xtensor/xarray.hpp>
#include <xtensor/xrandom.hpp>
#include <xtensor-blas/xlinalg.hpp>


std::vector<double> RunMatMultTest(int size = 100, int loops = 10)
{
    xt::random::seed(time(NULL));
    xt::xarray<double> A = xt::random::randn<double>({size, size});
    std::vector<double> duration_list(loops);
    for (int i = 0; i <= loops; ++i)
    {
        auto t0 = std::chrono::steady_clock::now();
        xt::xarray<double> B = xt::linalg::dot(A, A);
        auto t1 = std::chrono::steady_clock::now();
        std::chrono::duration<double> duration = t1 - t0;
        duration_list[i] = duration.count();
    }
    return duration_list;
}

int main()
{
    int size = 1000;
    int loops = 10;
    std::cout << "please entr the size of the suqare matrix, default is 1000" << std::endl;
    std::cin >> size;
    std::cout << "please entr the loop times, default is 10" << std::endl;
    std::cin >> loops;

    std::vector<double> duration_list = RunEigDecompTest(size, loops);
    for (int i=0; i<loops; ++i){
        std::cout << duration_list[i] << " s\n";
    }
    std::cout <<"The mean time is: ";
    std::cout <<std::accumulate(duration_list.begin(), duration_list.end(), 0.0) / loops << " s\n";
    std::cin.get();

    return 0;
}
```
It takes about $10.5$ s for the matrix with size $10000 \times 10000$.

### Eigenvalue Decomposition
```cpp
std::vector<double> RunEigDecompTest(int size = 100, int loops = 10)
{
    xt::random::seed(time(NULL));
    xt::xarray<double> A = xt::random::randn<double>({size, size});
    std::vector<double> duration_list(loops);
    for (int i = 0; i <= loops; ++i)
    {
        auto t0 = std::chrono::steady_clock::now();
        auto B = xt::linalg::eig(A);
        auto t1 = std::chrono::steady_clock::now();
        std::chrono::duration<double> duration = t1 - t0;
        duration_list[i] = duration.count();
    }
    return duration_list;
```
It takes about $0.12$ s for the matrix with size $500 \times 500$.
It takes about $0.62$ s for the matrix with size $1000 \times 1000$.
It takes about $22.6$ s for the matrix with size $4000 \times 4000$.
```ad-note
title: Hermitian Matrix Eigenvalue Decompostion
use `xt::linalg::eigh()`:
It takes about $0.014$ s for the matrix with size $500 \times 500$.
It takes about $0.077$ s for the matrix with size $1000 \times 1000$.
It takes about $4.8$ s for the matrix with size $4000 \times 4000$.
```

### QR decomposition
```cpp


```

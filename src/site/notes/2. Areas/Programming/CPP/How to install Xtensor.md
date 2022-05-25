---
{"dg-publish":true,"permalink":"/2-areas/programming/cpp/how-to-install-xtensor/","dgHomeLink":true,"dgPassFrontmatter":false}
---


Xtensor 系列主要包含 `xtl`, `xsimd`, `xtensor-blas`，`xtensor-fftw` 和 `xtensor`

## 1. Install `xtl`
1. git clone
2. make from source
```shell
mkdir build
cd build
cmake ..
sudo make install
```

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

在 `build` 下有一个 benchmark，名为 `mandelbrot`，其结果如下，可以发现采用 `AVX2` 指令时，速度最快

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
[[2. Areas/Programming/CPP/Xtensor tips#Performance test|例子]]



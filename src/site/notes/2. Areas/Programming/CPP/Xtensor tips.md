---
{"dg-publish":true,"permalink":"/2-areas/programming/cpp/xtensor-tips/","dgHomeLink":true,"dgPassFrontmatter":false}
---

## Installation
[[2. Areas/Programming/CPP/How to install Xtensor|2. Areas/Programming/CPP/How to install Xtensor]]

## Compile
```shell
> g++ xtensor_performance.cpp -o ./output/xtensor_performance -m64 -I ./libs  -I"${MKLROOT}/include"  -L${MKLROOT}/lib -Wl,-rpath=${MKLROOT}/lib -lmkl_intel_lp64 -lmkl_gnu_thread -lmkl_core -lgomp -lpthread -lm -ldl  -O3
```
where `${MKLROOT}` is referred to the installation location. See [[2. Areas/Programming/CPP/Eigen tips#2 Install Intel OneMKL using conda|eigen tips]] for details.

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

> [!note] Hermitian Matrix Eigenvalue Decomposition
> 
> use `xt::linalg::eigh ()`
> 
> 
> It takes about $0.014$ s for the matrix with size $500 \times 500$.
> It takes about $0.077$ s for the matrix with size $1000 \times 1000$.
> It takes about $4.8$ s for the matrix with size $4000 \times 4000$.

### QR factorization
```cpp
std::vector<double> RunQRTest(int size = 100, int loops = 10)
{
    xt::random::seed(time(NULL));
    xt::xarray<double> A = xt::random::randn<double>({size, size});
    std::vector<double> duration_list(loops);
    for (int i = 0; i <= loops; ++i)
    {
        auto t0 = std::chrono::steady_clock::now();
        auto B = xt::linalg::qr(A);
        auto t1 = std::chrono::steady_clock::now();
        std::chrono::duration<double> duration = t1 - t0;
        duration_list[i] = duration.count();
    }
    return duration_list;
}
```
It takes about $0.00096$ s for the matrix with size $100 \times 100$
It takes about $0.049$ s for the matrix with size $1000 \times 1000$
It takes about $24.2$ s for the matrix with size $10000 \times 10000$

While the ==numpy== version takes  $0.063$ s for the matrix with size $1000 \times 1000$, and $23.4$s for  the matrix with size $10000 \times 10000$.

### xt :: view
The codes are referenced from [Roman Poya](https://romanpoya.medium.com/a-look-at-the-performance-of-expression-templates-in-c-eigen-vs-blaze-vs-fastor-vs-armadillo-vs-2474ed38d982)
```cpp
template <typename T>
T finite_difference_range_impl(xt::xtensor<T,2> &u, int num)
{
    xt::xtensor<T, 2> u_old = u;
    view(u, range(1, num - 1), range(1, num - 1)) =
        ((view(u_old, range(0, num - 2), range(1, num - 1)) + view(u_old, range(2, num - 0), range(1, num - 1)) +
          view(u_old, range(1, num - 1), range(0, num - 2)) + view(u_old, range(1, num - 1), range(2, num - 0))) *
             4.0 +
         view(u_old, range(0, num - 2), range(0, num - 2)) + view(u_old, range(0, num - 2), range(2, num - 0)) +
         view(u_old, range(2, num - 0), range(0, num - 2)) + view(u_old, range(2, num - 0), range(2, num - 0))) /
        20.0;
    return xt::linalg::norm(u - u_old);
}

std::vector<double> RunFiniteDiffTest(int size = 100, int loops = 10)
{
    xt::random::seed(time(NULL));
    xt::xtensor<double, 2> A = xt::random::randn<double>({size, size});
    std::vector<double> duration_list(loops);
    for (int i = 0; i <= loops; ++i)
    {
        auto t0 = std::chrono::steady_clock::now();
        auto result = finite_difference_range_impl<double> (A, size);
        auto t1 = std::chrono::steady_clock::now();
        std::chrono::duration<double> duration = t1 - t0;
        duration_list[i] = duration.count();
    }
    return duration_list;
```
It takes about $2.2\times 10^{-4}$ s for the matrix with size $100 \times 100$
It takes about $6.3\times 10^{-3}$ s for the matrix with size $500 \times 500$
It takes about $2.6\times 10^{-2}$ s for the matrix with size $1000 \times 1000$

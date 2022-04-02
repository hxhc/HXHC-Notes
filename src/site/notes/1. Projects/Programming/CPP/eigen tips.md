---
{"dg-publish":true,"permalink":"/1-projects/programming/cpp/eigen-tips/"}
---
## 1. Install OpenBlas
Install gfortran first.
1. Download `openblas 0.3.20`
2. `tar -xzvf openblas*`
3. `cd openblas`
4. `make USE_OPENMP=1`，if openmp is not used, set 0.
5. `make install`

The output of  `make install` shows that Openblas in install in /opt/OpenBLAS
```shell
make -j 16 -f Makefile.install install
make[1]: Entering directory '/home/hxhc/Downloads/OpenBLAS-0.3.20'
Generating openblas_config.h in /opt/OpenBLAS/include
Generating f77blas.h in /opt/OpenBLAS/include
Generating cblas.h in /opt/OpenBLAS/include
Copying LAPACKE header files to /opt/OpenBLAS/include
Copying the static library to /opt/OpenBLAS/lib
Copying the shared library to /opt/OpenBLAS/lib
Generating openblas.pc in /opt/OpenBLAS/lib/pkgconfig
Generating OpenBLASConfig.cmake in /opt/OpenBLAS/lib/cmake/openblas
Generating OpenBLASConfigVersion.cmake in /opt/OpenBLAS/lib/cmake/openblas
Install OK!
```
It takes about $9.4$ s for the matrix with size $10000 \times 10000$.

## 2. Install Intel OneMKL using conda
1. Use  `mamba install -c intel mkl-devel==2022.0.1` to install the mkl library with the specific version. And other dependencies will also be installed.

| Lib                       | Version                        | Size       |
|:--------------------------|:-------------------------------|:-----------|
| intel-openmp&nbsp; | 2022.0.1 intel_3633      | 8 MB |
| mkl                 | 2022.0.1 intel_117&nbsp; |     199 MB |
| mkl-devel                 | 2022.0.1 intel_117 |      15 KB |
| mkl-include               |       2022.0.1&nbsp; intel_117 |     726 KB |
| tbb&nbsp;           | 2021.5.0&nbsp; intel_707&nbsp; |       1 MB |
2. add `export MKLROOT=/home/hxhc/miniconda3` to . bashrc or . zshrc

## 3. Eigen performance test
### 3.1 Compile
``` ad-important
title: Compile instruction
- *MKL linking*
The link command can be referenced on [Intel MKL Link Line Advisor](https://www.intel.com/content/www/us/en/developer/tools/oneapi/onemkl-link-line-advisor.html#gs.vxcfgk)
~~~shell
> g++ eigen_performance.cpp -o ./output/eigen_performance -m64 -I ./libs  -I"${MKLROOT}/include"  -L${MKLROOT}/lib -Wl,-rpath=${MKLROOT}/lib -lmkl_intel_lp64 -lmkl_gnu_thread -lmkl_core -lgomp -lpthread -lm -ldl -march=native -O3 
~~~
Note that there is ==no space== in `-Wl,-rpath=${MKLROOT}/lib`

- *Openblas linking*
~~~shell
g++ eigen_performance.cpp -o ./output/eigen_performance -I ./libs  -I/opt/OpenBLAS/include  -L/opt/OpenBLAS/lib -Wl,-rpath=/opt/OpenBLAS/lib -lopenblas -march=native -O3  
~~~
```


### 3.2 Matrix multiplication test

```cpp
#define EIGEN_USE_BLAS
#define EIGEN_USE_LAPACK
#define EIGEN_USE_MKL_ALL

#include <iostream>
#include <chrono>
#include <Eigen/Dense>
#include <EigenRand/EigenRand>
#include <vector>
#include <fstream>

Eigen::ArrayXd RunMatMultTest(int size=100, size_t loops=10)
{
    Eigen::Rand::Vmt19937_64 urng{uint (size)};
    Eigen::Rand::StdNormalGen<double> stdnorm;
    Eigen::ArrayXd duration_list(loops);
    Eigen::MatrixXd A = stdnorm.generate<Eigen::MatrixXd>(size, size, urng);
    
    for (size_t i = 0; i < loops; ++i)
    {
        auto t0 = std::chrono::steady_clock::now();
        Eigen::MatrixXd B = A * A;
        auto t1 = std::chrono::steady_clock::now();
        std::chrono::duration<double> duration = t1 - t0;
        duration_list[i] = duration.count();
    }
    return duration_list;
}

int main(){
    int size = 100;
    std::cout << "please entr the size of the suqare matrix" << std::endl;
    std::cin >> size;
    int loops = 10;
    // auto t0 = std::chrono::steady_clock::now();
    Eigen::ArrayXd duration_list = RunMatMultTest(size, loops);
    // auto t1 = std::chrono::steady_clock::now();
    // auto duration = std::chrono::duration_cast<std::chrono::milliseconds>(t1 - t0);
    // std::cout << duration.count()/loops <<" ms\n";
    std::cout << duration_list << "\n";
    std::cout <<"The mean time is: ";
    std::cout << duration_list.mean() << "\n";
    std::cin.get();
    return 0;
}
```
Linked to MKL, it takes about $14$ ms for the matrix with size  $1000\times 1000$，and takes about $11$ s for the matrix with size $10000 \times 10000$. While linked to OpenBLAS, it takes about 9.4s.

## 3.3 Eigenvalue decomposition
```cpp
Eigen::ArrayXd RunEigDecompTest(int size=100, int loops=10)
{
    Eigen::Rand::Vmt19937_64 urng{uint (size)};
    Eigen::Rand::StdNormalGen<double> stdnorm;
    Eigen::ArrayXd duration_list(loops);
    Eigen::MatrixXd A = stdnorm.generate<Eigen::MatrixXd>(size, size, urng);
    Eigen::MatrixXd COV = A*A.transpose();
    
    for (int i = 0; i < loops; ++i)
    {
        auto t0 = std::chrono::steady_clock::now();
        Eigen::SelfAdjointEigenSolver<Eigen::MatrixXd> eigensolver(A);
        Eigen::MatrixXcd B = eigensolver.eigenvalues();
        auto t1 = std::chrono::steady_clock::now();
        std::chrono::duration<double> duration = t1 - t0;
        duration_list[i] = duration.count();
    }
    return duration_list;
}
```
Linked to MKL, it takes about $0.03$ s for the matrix with size  $500\times 500$
Linked to MKL, it takes about $0.19$ s for the matrix with size  $1000\times 1000$
Linked to MKL, it takes about $8.5$ s for the matrix with size  $4000\times 4000$

```ad-note
title: EigenSolvers

Note that there are different EigenSolers implemented by ==Eigen==. In the code above, the ==SelfAdjointEigenSolver== is used for Hermitian (self-adjoint) matrix, which includes the covariance matrix in real applications. While the ==SelfAdjointEigenSolver== is usually very fast. The corresponding versions of ==SelfAdjointEigenSolver==in ==Numpy== and ==Xtensor== are ==np.linalg.eigh()== and ==xt::linalg::eigh()==, respectively. But the ==Eigen== implementation is slower than [[1. Projects/Programming/CPP/Xtensor tips#Eigenvalue Decomposition|Xtensor]].

However, for a general EigenSolver (==EigenSolver==), the speed is slower. For $500\times 500$, the time is $0.10$ s; For $1000\times 1000$, the time is $0.63$ s; For $4000\times 4000$, the time is $46$ s, which is slower than ==Numpy== and [[1. Projects/Programming/CPP/Xtensor tips#Eigenvalue Decomposition|Xtensor]].
```

## 3.4 QR decomposition
---
{"dg-publish":true,"permalink":"/2-areas/programming/cpp/armadillo-tips/","dgHomeLink":true,"dgPassFrontmatter":false}
---


## 1. CSV file loading 
1. The armadillo documentation specifies

> [!important] Note
> Using batch insertion constructors is generally much faster than consecutively inserting values using element access operators

So here is the best I could come up with

```cpp
sp_mat get(const char *filename) {         
    vector<long long unsigned int> location_u;
    vector<long long unsigned int> location_m;
    vector<double> values;                    

    ifstream file(filename);                  
    int a, b, c;                              
    while(file >> a >> b >> c) {                                   
        location_u.push_back(a);              
        location_m.push_back(b);              
        values.push_back(c);                  
    }                                         

    umat lu(location_u);                      
    umat lm(location_m);                      
    umat location(join_rows(lu, lm).t());     

    return V(location, vec(values));                                         
}                                             
```

It now runs at a reasonable speed, at about 1 million lines a second.


2. For example, if you're trying to do this, it will take soo much time to load the file:
```cpp
arma::mat A;
A.load("file.csv", arma::csv_ascii);
```
So this is an alternative, which is thousand more faster than above code:
```Cpp
arma::mat readCSV(const std::string &filename, const std::string &delimeter = ",")
{
    std::ifstream csv(filename);
    std::vector<std::vector<double>> datas;

    for(std::string line; std::getline(csv, line); ) {

        std::vector<double> data;

        // split string by delimeter
        auto start = 0U;
        auto end = line.find(delimeter);
        while (end != std::string::npos) {
            data.push_back(std::stod(line.substr(start, end - start)));
            start = end + delimeter.length();
            end = line.find(delimeter, start);
        }
        data.push_back(std::stod(line.substr(start, end)));
        datas.push_back(data);
    }

    arma::mat data_mat = arma::zeros<arma::mat>(datas.size(), datas[0].size());

    for (int i=0; i<datas.size(); i++) {
        arma::mat r(datas[i]);
        data_mat.row(i) = r.t();
    }

    return data_mat;
}
```

---
## 2. The same random number in openmmp loop
As a workaround, to generate a set of 100 random values I suggest to avoid calling randn () to obtain each value, and instead use randn (100) to obtain 100 values is one hit:

``` cpp
vec X = randn(100);
```

If the requested number of random values exceeds a threshold (>= 1024) and openmp is enabled, Armadillo should internally use multi-threading to speed up the generation of the random values. In this case each thread should use a ==separate seed==. The internal code for obtaining a _set_ of randn values is here: [https://gitlab.com/conradsnicta/armadillo-code/-/blob/10.7.x/include/armadillo_bits/arma_rng.hpp#L535-582](https://gitlab.com/conradsnicta/armadillo-code/-/blob/10.7.x/include/armadillo_bits/arma_rng.hpp#L535-582) (don't call this function directly, as it's subject to change -- it's not part of the public API)

 If you still need to use randn() to obtain each random value individually within a parallel for loop, you could try manually setting the seed for each thread beforehand. Use the `arma_rng::set_seed(value)` function.

The internal code for obtaining an _individual_ randn value (not a _set_ of randn values) is here: [https://gitlab.com/conradsnicta/armadillo-code/-/blob/10.7.x/include/armadillo_bits/arma_rng.hpp#L449-470](https://gitlab.com/conradsnicta/armadillo-code/-/blob/10.7.x/include/armadillo_bits/arma_rng.hpp#L449-470) In this code, `mt19937_64_instance` comes from libarmadillo.so (the armadillo runtime library), defined here: [https://gitlab.com/conradsnicta/armadillo-code/-/blob/10.7.x/src/wrapper1.cpp#L34-47](https://gitlab.com/conradsnicta/armadillo-code/-/blob/10.7.x/src/wrapper1.cpp#L34-47) `mt19937_64_instance` is defined as a thread_local object. Due to this definition, each instance of `mt19937_64_instance` has the same seed for each thread by default. The seed can't be random, as that would defeat reproducibility (ie. each time a user program is executed, it should generate the same results).


## Performance Test
```cpp
#define ARMA_DONT_USE_WRAPPER
#define ARMA_USE_OPENMP

#include <iostream>
#include <chrono>
#include <armadillo>
#include <ctime>
#include <vector>

std::vector<double> RunTest(int size = 100, int loops = 10)
{
    arma::mat A = arma::mat(size, size, arma::fill::randn);
    std::vector<double> duration_list(loops);
    for (int i = 0; i <= loops; ++i)
    {
        arma::wall_clock timer;
        timer.tic();
        arma::mat B = A * A;
        double n = timer.toc();
        duration_list[i]=n;
    }
    return duration_list;

}
```
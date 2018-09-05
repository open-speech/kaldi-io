## KALDI-IO lib
> Kaldi c++ IO lib (static and dynamic). 
> 
> It can be used as "kaldi I/O C++ API". Using this library, 
> "*.ark,*.scp" formats can be read and write,
> and kaldi::[Matrix|Vector|..] is available.


#### Compile
- requirements:
    - cmake >= 3.0
    - one of blas math lib:
        - atlas
            - ubuntu: `sudo apt-get install libatlas3-base`
        - mkl
            - If in conda, to install mkl: `conda install mkl`
        - Accelerate framework(Darwin)
        - ...

```bash
cd kaldi-io
mkdir build && cd build
cmake -DCMAKE_INSTALL_PREFIX=.. .. # set install prefix as kaldi-io
make
make install 
```
#### Results:
- kaldi-io/lib:
    - libkaldi_io_static.a 
    - libkaldi_io_shared.so
- kaldi-io/include:
    - kaldi-io.h (the one big header, can be modified by need)
    - sub header dirs...

#### Usage
- Include the header dir and link the lib

#### About
- The BLAS math dependency is solved automatically from system path, by cmake `find_package`(cmake/Modules/FindBLAS.cmake)
    - MKL/ATLAS/Accelerate(osx) is prefered
    - if you want to set specific math lib:
    ```bash
    # make sure build dir is clean
    cmake -DBLAS_VENDORS=[ATLAS|MKL|OPEN|..] ..
    # additioinally, custom math lib search path can be set, for example:
    cmake -DBLAS_VENDORS=ATLAS -DBLAS_ATLAS_LIB_DIRS=.../atlas/build/lib ..
    cmake -DBLAS_VENDORS=MKL -DBLAS_MKL_LIB_DIRS=/opt/intel/mkl/lib/intel64 ..
    ```

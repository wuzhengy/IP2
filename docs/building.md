#### In order to build libip2, please export a few environment variables. The current version only support linux x86, however the base libtorrent supports arm and intel, we will make changes accordingly. 

## ip2 lib name shall be lower case
## Basic env
Recommend to use linux x86-64 and ubuntu 18.04

### Setup gcc
```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt update
sudo apt install gcc-10 g++-10
```
The version of gcc has to be 10+, some system conflicts gcc symbolic link to gcc-7, need to verify . 

### Setup boost
```
export BOOST_DOT_VERSION="1.76.0"
export BOOST_VERSION="1_76_0"
```

```
Steps: 
1. get boost tar ball: wget -nv -O boost.tar.gz https://boostorg.jfrog.io/artifactory/main/release/${BOOST_DOT_VERSION}/source/boost_${BOOST_VERSION}.tar.gz
2. tar xzf boost.tar.gz
3. mv boost_${BOOST_VERSION} boost  #boost 
4. cd boost  
5. ./bootstrap.sh --with-toolset=gcc  #boost use bootstrap to build intial b2 excutable; you can furthur do ./b2 and ./b2 install to complete boost lib, but with b2, it is sufficient to compile libip2
```

### Setup openssl
```
export OPENSSL_VERSION="1.1.1k"
export OPENSSL_NO_OPTS="no-deprecated no-shared no-makedepend no-static-engine no-stdio no-posix-io no-threads no-ui-console no-zlib no-zlib-dynamic -fno-strict-aliasing -fvisibility=hidden -O3"
```
```
1. wget -nv -O openssl.tar.gz https://www.openssl.org/source/openssl-${OPENSSL_VERSION}.tar.gz
2. tar xzf openssl.tar.gz
3. cd openssl-${OPENSSL_VERSION}
4. ./Configure linux-x86_64 ${OPENSSL_NO_OPTS} -fPIC --prefix=${PWD}/../openssl
5. make 
6. make install_sw 
```
### Setup leveldb
```
1. wget -nv -O leveldb.tar.gz https://github.com/google/leveldb/archive/refs/tags/1.23.tar.gz
2. tar xzf leveldb.tar.gz
3. cd leveldb
4. modify CMakeLists.txt
```
# make compile to be shared you need to enable fPIC by following in the file. 
add_compile_options(-fPIC) into top space of the file
# make following options changes
option(LEVELDB_BUILD_TESTS "Build LevelDB's unit tests" OFF)
option(LEVELDB_BUILD_BENCHMARKS "Build LevelDB's benchmarks" OFF)
```
5. mkdir build
6. cd build
7. cmake -D CMAKE_INSTALL_PREFIX=${PWD}/../../leveldb  ../
8. make
9. make install
```
### Setup snappy
```
1. wget -nv -O snappy.tar.gz https://github.com/google/snappy/archive/refs/tags/1.1.8.tar.gz
2. tar xzf snappy.tar.gz
3. cd snappy
4. modify CMakeLists.txt
```
# make compile to be shared you need to enable fPIC by following in the file. 
add_compile_options(-fPIC) into top space of the file
# make following options changes
option(SNAPPY_BUILD_TESTS "Build Snappy's own tests" OFF)

```

5. mkdir build
6. cd build
7. cmake -D CMAKE_INSTALL_PREFIX=${PWD}/../../snappy  ../
8. make
9. make install
```
### Setup sqlite
```
1. wget -nv -O sqlite.tar.gz https://sqlite.org/2021/sqlite-autoconf-3360000.tar.gz
2. tar xzf sqlite.tar.gz
3. cd sqlite
4. ./configure --prefix=${SQLITE_ROOT} CFLAGS=-fPIC CXXFLAGS=-fPIC
5. make
6. make install
```

## Build
### Environment variables
```
export  BOOST_ROOT=${PWD}/boost
export  OPENSSL_ROOT=${PWD}/openssl
export  LEVELDB_ROOT=${PWD}/leveldb
export  SQLITE_ROOT=${PWD}/sqlite
export  IP2_ROOT=${PWD}/ip2
# you might need to rename IP2 directory to ip2, package name shall be all small case, the github repo has make is upper, which need to be fixed. 
```
### Compile
```
${BOOST_ROOT}/b2
```
### Install
The ip2 lib need to write libs to system folders
```
sudo -E ${BOOST_ROOT}/b2 install
```

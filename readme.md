# YahOS toolchain

This repo contains patches for binutils, gcc and newlib to support targetting my hobby operating system YahOS  
These patches are against the following:

* gcc 5.3.0
* binutils 2.26
* newlib 2.3.0.20160104

to set up the toolchain do something similar to the following:  

first lets export some envvars

```sh
export TARGET=i686-yahos 
export PREFIX=/opt/yahos-toolchain
```

and untar the packages we need 

```sh
tar xvf {gcc-5.3.0.tar.gz, binutils-2.26.tar.gz, newlib-2.3.0.20160104}
cd gcc-5.3.0 && patch < ../2-gcc-yahos.patch
cd binutils-2.26 && patch < ../1-binutils-yahos.patch
cd newlib-2.3.0.20160104 && patch < ../3-newlib-yahos.patch
```

now lets build binutils targeted for i386-yahos

```sh
mkdir binutils-build && cd binutils-build
../binutils-2.26/configure --with-sysroot --disable-nls --disable-werror --target=$TARGET --prefix=$PREFIX
make
make install
cd ..
```

now we can build the first phase of gcc in order to build newlib 

```sh
mkdir gcc-stage1-build && cd gcc-stage1-build
../gcc-5.3.0/configure --disable-nls --target=$TARGET --prefix=$PREFIX --with-gnu-as --with-gnu-ld --with-newlib --without-headers
make all-gcc
make install-gcc
cd ..
```

now we can build newlib

```sh
mkdir newlib-build && cd newlib-build
../newlib-2.3.0.20160104/configure --target=$TARGET --prefix=$PREFIX
make all
make install
cd ..
```

and lastly the final version of gcc that utilises the newlib we built above

```sh
export PATH=$PATH:$PREFIX/bin

mkdir gcc-stage2-build && cd gcc-stage2-build 
../gcc-5.3.0/configure --disable-nls --target=$TARGET --prefix=$PREFIX --with-gnu-as --with-gnu-ld --with-newlib --disable-shared --disable-libssp --enable-languages=c,c++
make
make install
cd ..
```

Congratulations, you can now hopefully compile applications for YahOS! using `i386-yahos-gcc` and friends, test it out by running make in this folder.

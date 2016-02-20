# YahOS toolchain

This repo contains patches for binutils, gcc and newlib to support targetting my hobby operating system YahOS  
These patches are against the following:

* gcc 5.3.0
* binutils 2.26
* newlib 2.3.0.20160104

to set up the toolchain do something similar to the following:  

```
export TARGET=i386-yahos 
export PREFIX=/opt/yahos-toolchain

tar xvf {gcc-5.3.0.tar.gz, binutils-2.26.tar.gz, newlib-2.3.0.20160104}
cd gcc-5.3.0 && patch < ../2-gcc-yahos.patch
cd binutils-2.26 && patch < ../1-binutils-yahos.patch
cd newlib-2.3.0.20160104 && patch < ../3-newlib-yahos.patch

mkdir binutils-build && cd binutils-build
../binutils-2.26/configure -with-sysroot --disable-nls --disable-werror --target=$TARGET --prefix=$PREFIX
make
make install
cd ..

mkdir gcc-stage1-build && cd gcc-stage1-build
../gcc-5.3.0/configure --disable-nls --target=$TARGET --prefix=$PREFIX --with-gnu-as --with-gnu-ld --with-newlib --without-headers
make all-gcc
make install-gcc
cd ..

mkdir newlib-build && cd newlib-build
../newlib-2.3.0.20160104/configure --target=$TARGET --prefix=$PREFIX
make all
make install
cd ..

export PATH=$PATH:$PREFIX/bin

mkdir gcc-stage2-build && cd gcc-stage2-build 
../gcc-5.3.0/configure --disable-nls --target=$TARGET --prefix=$PREFIX --with-gnu-as --with-gnu-ld --with-newlib --disable-shared --disable-libssp --enable-languages=c,c++
make
make install
cd ..
```

Congratulations, you can now hopefully compile applications for YahOS! using `i386-yahos-gcc` and friends.

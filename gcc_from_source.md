cd ~
tar -xvf gcc-releases-gcc-11.1.0.tar.gz

cd gcc-releases-gcc-11.1.0
contrib/download_prerequisites

sudo apt install flex

cd ~ 
mkdir build
cd build

Configure GCC for compilation:
../gcc-releases-gcc-11.1.0/configure -v --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu --prefix=/usr/local/gcc-11.1.0 --enable-checking=release --enable-languages=c,c++,fortran --disable-multilib --program-suffix=-11.1

i386-linux-gnu
../gcc-11.2.8/configure -v --build=i386-linux-gnu --host=i386-linux-gnu --target=i386-linux-gnu --prefix=/usr/local/gcc-11.2.8 --enable-checking=release --enable-languages=c,c++,fortran --disable-multilib --program-suffix=-11.2

make -j 8 (8 - количество потоков)

sudo make install-strip

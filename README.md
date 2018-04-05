# domino

An attempt of running domino program following SIGCOMM'16 "Packet Transactions".

## Hands-on Steps

### Install Dependencies:

1.Install packages:

```
sudo apt-get install build-essential autotools-dev libncurses5-dev autoconf libtool and zlib1g-dev
```

2.Install clang+llvm:

Download clang + llvm from http://llvm.org/releases/3.5.0/clang+llvm-3.5.0-x86_64-linux-gnu-ubuntu-14.04.tar.xz ;

```
// convert .tar.xz to .tar
xz -d clang+llvm-3.5.0-x86_64-linux-gnu-ubuntu-14.04.tar.xz

// extraction
tar -xvf clang+llvm-3.5.0-x86_64-linux-gnu-ubuntu-14.04.tar
```

3.Install sketch:

Download sketch from https://people.csail.mit.edu/asolar/sketch-1.6.9.tar.gz ;

```
// extraction
tar -zxvf sketch-1.6.9.tar.gz

// install
cd sketch-1.6.9
cd sketch-backend
chmod +x ./configure
./configure
make
cd ..

// test sketch
cd sketch-frontend
chmod +x ./sketch
./sketch test/sk/seq/miniTest1.sk

// set path of sketch, under sketch-frontend directory:
export PATH="$PATH:`pwd`"
export SKETCH_HOME="`pwd`/runtime"

// ** option **, run all sketch tests
cd test/sk/seq
make long
```

### Install Banzai

```
git clone https://github.com/packet-transactions/banzai.git
cd banzai
./autogen.sh && ./configure && sudo make install
```

### Install Domino Compiler

**Hint: `CLANG_DEV_LIBS` in my environment is `/home/sdn/clang+llvm-3.5.0-x86_64-linux-gnu`.**

```
git clone https://github.com/packet-transactions/domino-compiler.git
cd domino-compiler/
./autogen.sh
./configure CLANG_DEV_LIBS=/home/sdn/clang+llvm-3.5.0-x86_64-linux-gnu
make
make check
```

# domino

An attempt of running domino program following SIGCOMM'16 "Packet Transactions".

## Hands-on Steps

### Install Dependencies:

1.Install g++-5 (reference: [How to install the latest g++(currently 5.1) in Ubuntu(currently 14.04)?](https://askubuntu.com/questions/618474/how-to-install-the-latest-gcurrently-5-1-in-ubuntucurrently-14-04)):

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install gcc-5 g++-5
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-5 60 --slave /usr/bin/g++ g++ /usr/bin/g++-5
```

2.Install packages:

```
sudo apt-get install build-essential autotools-dev libncurses5-dev autoconf libtool and zlib1g-dev
```

3.Install clang+llvm:

Download clang + llvm from http://llvm.org/releases/3.5.0/clang+llvm-3.5.0-x86_64-linux-gnu-ubuntu-14.04.tar.xz ;

```
// convert .tar.xz to .tar
xz -d clang+llvm-3.5.0-x86_64-linux-gnu-ubuntu-14.04.tar.xz

// extraction
tar -xvf clang+llvm-3.5.0-x86_64-linux-gnu-ubuntu-14.04.tar
```

4.Install sketch:

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

### Try a Domino Example

Clone the domino examples:

```
git clone https://github.com/packet-transactions/domino-examples.git
cd domino-examples
```

Try flowlet introduced in SIGCOMM paper:

```
./compile.sh domino_programs/flowlets.c banzai_atoms/pair.sk 30 10
```

The output mapping is avaiable at "/tmp/pipeline.png":

```
Found a mapping
Warning: /tmp/error.log: syntax error in line 1 near '.'
Pipeline diagram at /tmp/pipeline.png
```

Try `run_expt.py`:

```
python run_expts.py domino_programs.list atom_templates.list
```

## Known Issues

1.Install domino compiler. As executing `make`: "experimental/tuple: No such file or directory":

```
In file included from domino.cc:33:0:
compiler_pass.h:10:30: fatal error: experimental/tuple: No such file or directory
 #include <experimental/tuple>
                              ^
compilation terminated.
make[2]: *** [domino-domino.o] Error 1
make[2]: Leaving directory `/home/sdn/domino-compiler'
make[1]: *** [all-recursive] Error 1
make[1]: Leaving directory `/home/sdn/domino-compiler'
make: *** [all] Error 2
```

This bug can be reproduced when you are using `g++` which version <= 5.0. Updating your g++ can resolve this problem.

2.As executing : subprocess.Popen "OSError: [Errno 2] No such file or directory"

```
Traceback (most recent call last):
  File "run_expts.py", line 53, in <module>
    all_codelets_mapped = check_compile(domino_file, atom_template, True);
  File "run_expts.py", line 10, in check_compile
    out,err = program_wrapper(program = ["domino", domino_file, atom_template, "30", "10", "yes" if run_preproc else ""])
  File "run_expts.py", line 27, in program_wrapper
    sp = subprocess.Popen(program, stdout = t_stdout, stderr = t_stderr)
  File "/usr/lib/python2.7/subprocess.py", line 710, in __init__
    errread, errwrite)
  File "/usr/lib/python2.7/subprocess.py", line 1327, in _execute_child
    raise child_exception
OSError: [Errno 2] No such file or directory
```

To resolve this problem, you should add an extra parameter 'shell=True' to the Popen call at [run_expts.py#L27](https://github.com/packet-transactions/domino-examples/blob/master/run_expts.py#L27). 

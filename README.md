# carla_source_build_2025
source build of carla 9.12 etc in 2025 on Ubuntu 20.04

* Note: I would recommend not to install it from scracth unless needed.
The pain is mainly due to GCC/Clang


## clang/gcc related
1. install clang8 leads to error
```
clang-8 : Depends: libllvm8 (>= 1:8~svn298832-1~) but it is not going to be installed Depends: libstdc++-5-dev but it is not installable Depends: libgcc-5-dev but it is not installable Depends: libobjc-5-dev but it is not installable Depends: libclang-common-8-dev (= 1:8.0.1+svn369350-1~exp1~20200112113617.82) but it is not going to be installed Depends: libclang1-8 (= 1:8.0.1+svn369350-1~exp1~20200112113617.82) but it is not going to be installed Recommends: llvm-8-dev but it is not going to be installed Recommends: libomp-8-dev but it is not going to be installed lld-8 : Depends: libllvm8 (= 1:8.0.1+svn369350-1~exp1~20200112113617.82) but it is not going to be installed 
```
Solution: 
After removing "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-8 main" from /etc/apt/sources.list and running apt-get update i was able to install clang-8

2. /usr/bin/clang++-8" is not able to compile a simple test program
```

    "/usr/bin/clang++-8"

  is not able to compile a simple test program.

  It fails with the following output:

    Change Dir: /home/paceholder/carla/carla/Build/llvm-8.0-build/CMakeFiles/CMakeScratch/TryCompile-BqtZWP
    
    Run Build Command(s):/usr/bin/ninja -v cmTC_75542 && [1/2] /usr/bin/clang++-8    -MD -MT CMakeFiles/cmTC_75542.dir/testCXXCompiler.cxx.o -MF CMakeFiles/cmTC_75542.dir/testCXXCompiler.cxx.o.d -o CMakeFiles/cmTC_75542.dir/testCXXCompiler.cxx.o -c /home/paceholder/carla/carla/Build/llvm-8.0-build/CMakeFiles/CMakeScratch/TryCompile-BqtZWP/testCXXCompiler.cxx
    [2/2] : && /usr/bin/clang++-8   CMakeFiles/cmTC_75542.dir/testCXXCompiler.cxx.o -o cmTC_75542   && :
    FAILED: cmTC_75542 
    : && /usr/bin/clang++-8   CMakeFiles/cmTC_75542.dir/testCXXCompiler.cxx.o -o cmTC_75542   && :
    /usr/bin/ld: cannot find -lstdc++
    clang: error: linker command failed with exit code 1 (use -v to see invocation)
    ninja: build stopped: subcommand failed.
    
    

  

  CMake will not be able to correctly generate this project.
Call Stack (most recent call first):
  CMakeLists.txt:39 (project)


-- Configuring incomplete, errors occurred!
make: *** [Util/BuildTools/Linux.mk:137: setup] Error 1
```
I tried everything in https://github.com/carla-simulator/carla/issues/2792
None of it worked for me.

* Check  gcc installed using 

``` 
sudo apt-get purge gcc # (you can tap here to see what you have installed)
# you can also use grep
```
I would not suggest removing all gcc as some lib may depend on it (boost etc.)


* Ensure gcc 8 etc is installed.
```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test -y
sudo apt update
sudo apt install gcc-8 g++-8 libstdc++-8-dev -y
```
* Run a test code to ensure clang is ok
```
ls /usr/lib/gcc/x86_64-linux-gnu/8/libstdc++.so
echo 'int main() { return 0; }' > test.cpp
clang++-8 test.cpp -o test -v
```

This gives

```
clang version 8.0.1-9 (tags/RELEASE_801/final)
Target: x86_64-pc-linux-gnu
Thread model: posix
InstalledDir: /usr/bin
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/11
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/7
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/7.5.0
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/8
Found candidate GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/9
Found candidate GCC installation: /usr/lib/gcc/x86_64-linux-gnu/11
Found candidate GCC installation: /usr/lib/gcc/x86_64-linux-gnu/7
Found candidate GCC installation: /usr/lib/gcc/x86_64-linux-gnu/7.5.0
Found candidate GCC installation: /usr/lib/gcc/x86_64-linux-gnu/8
Found candidate GCC installation: /usr/lib/gcc/x86_64-linux-gnu/9
Selected GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/11
Candidate multilib: .;@m64
Selected multilib: .;@m64
Found CUDA installation: /usr/local/cuda-12.4, version 7.0
 "/usr/lib/llvm-8/bin/clang" -cc1 -triple x86_64-pc-linux-gnu -emit-obj -mrelax-all -disable-free -disable-llvm-verifier -discard-value-names -main-file-name test.cpp -mrelocation-model static -mthread-model posix -mdisable-fp-elim -fmath-errno -masm-verbose -mconstructor-aliases -munwind-tables -fuse-init-array -target-cpu x86-64 -dwarf-column-info -debugger-tuning=gdb -v -resource-dir /usr/lib/llvm-8/lib/clang/8.0.1 -internal-isystem /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++ -internal-isystem /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/x86_64-linux-gnu -internal-isystem /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/backward -internal-isystem /usr/include/clang/8.0.1/include/ -internal-isystem /usr/local/include -internal-isystem /usr/lib/llvm-8/lib/clang/8.0.1/include -internal-externc-isystem /usr/include/x86_64-linux-gnu -internal-externc-isystem /include -internal-externc-isystem /usr/include -fdeprecated-macro -fdebug-compilation-dir /home/paceholder/carla/carla -ferror-limit 19 -fmessage-length 211 -fobjc-runtime=gcc -fcxx-exceptions -fexceptions -fdiagnostics-show-option -fcolor-diagnostics -o /tmp/test-c410ec.o -x c++ test.cpp -faddrsig
clang -cc1 version 8.0.1 based upon LLVM 8.0.1 default target x86_64-pc-linux-gnu
ignoring nonexistent directory "/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/x86_64-linux-gnu"
ignoring nonexistent directory "/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/backward"
ignoring nonexistent directory "/include"
ignoring duplicate directory "/usr/include/clang/8.0.1/include"
#include "..." search starts here:
#include <...> search starts here:
 /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++
 /usr/include/clang/8.0.1/include
 /usr/local/include
 /usr/include/x86_64-linux-gnu
 /usr/include
End of search list.
 "/usr/bin/ld" --build-id --eh-frame-hdr -m elf_x86_64 -dynamic-linker /lib64/ld-linux-x86-64.so.2 -o test /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../x86_64-linux-gnu/crt1.o /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../x86_64-linux-gnu/crti.o /usr/bin/../lib/gcc/x86_64-linux-gnu/11/crtbegin.o -L/usr/bin/../lib/gcc/x86_64-linux-gnu/11 -L/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../x86_64-linux-gnu -L/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../lib64 -L/lib/x86_64-linux-gnu -L/lib/../lib64 -L/usr/lib/x86_64-linux-gnu -L/usr/lib/../lib64 -L/usr/lib/x86_64-linux-gnu/../../lib64 -L/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../.. -L/usr/lib/llvm-8/bin/../lib -L/lib -L/usr/lib /tmp/test-c410ec.o -lstdc++ -lm -lgcc_s -lgcc -lc -lgcc_s -lgcc /usr/bin/../lib/gcc/x86_64-linux-gnu/11/crtend.o /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../x86_64-linux-gnu/crtn.o
/usr/bin/ld: cannot find -lstdc++
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

Meaning that clang++-8 is trying to use GCC 11’s libstdc++, but it cannot find the actual library file (libstdc++.so) in the search paths.


```
ls /usr/lib/gcc/x86_64-linux-gnu/11/libstdc++.*
sudo apt install --reinstall libstdc++-11-dev

```
Which fixed the probelm........

### Carla asset file link issue
1. CARLA S3 amazon web services IS NOT AVAILABLE ANYMORE. 
You need to change manually new s3 backblaze services
https://carla-assets.s3.us-east-005.backblazeb2.com/20231108_c5101a5.tar.gz



###  gzip: stdin: not in gzip format

```
2025-07-29 02:04:58 (9.69 MB/s) - ‘boost_1_72_0.tar.gz’ saved [11533/11533]

Setup.sh: Extracting boost for Python 3.8.

gzip: stdin: not in gzip format
tar: Child returned status 1
tar: Error is not recoverable: exiting now
make: *** [Util/BuildTools/Linux.mk:137: setup] Error 2

```
 





# Ref

* https://github.com/carla-simulator/carla/issues/7192#issuecomment-2179214403
* https://github.com/carla-simulator/carla/issues/3441#issuecomment-713469615
* https://github.com/carla-simulator/carla/issues/2792

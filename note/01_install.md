# Install & Setup environment
Follow this [link](https://github.com/google/breakpad#getting-started-from-master "Install google-breakpad") to install google-breakpad

google-breakpad provides two tools: `dump_syms` & `minidump_stackwalk`

Setup environment `PATH` for them:

```bash
# google-breakpad dump_syms path
export PATH="breakpad_src_path/src/src/tools/linux/dump_syms/:${PATH}"
# google-breakpad minidump_stackwalk path
export PATH="breakpad_src_path/src/src/processor/:${PATH}"
```

# Modify CMakeList.txt
* Link breakpad static library with your application (/usr/local/lib/libbreakpad_client.a)
```cmake
FIND_LIBRARY(BREAKPAD_LIB NAMES breakpad_client)
target_link_libraries(your_application
  …
  ${BREAKPAD_LIB}
)
```
* Set breakpad header path (/usr/local/include/breakpad/)
```cmake
set(BREAKPAD_INCLUDE_DIRS /usr/local/include/breakpad/)
include_directories(
  …
  ${BREAKPAD_INCLUDE_DIRS}
)
```

# Integrating Breakpad into your Application
Follow this [link](https://chromium.googlesource.com/breakpad/breakpad/+/master/docs/linux_starter_guide.md#integrating-breakpad-into-your-application)

# Compile your Application
Compile your application with `-g` to include debugging sysbols, there are two ways:

* In CMakeList.txt
```cmake
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g")
set(CMAKE_BUILD_TYPE Debug)
```
* By build command
```bash
$ catkin_make -DCMAKE_BUILD_TYPE=Debug
```

# Producing symbols for your Application
Suppose our application produces one binary `our_binary` and one library `libour_library.so`.

First, we have to use `dump_syms` to produce symbol tables for both of them.

```bash
$ dump_syms our_binary > our_binary.sym
$ dump_syms libour_library.so > libour_library.so.sym
```

Second, build directory structure accroading to the first line of these symbol tables (your output will vary):
```bash
$ head -n1 our_binary.sym
MODULE Linux x86_64 985D8FC9E15C8ED4B7A51CCE03C5CA9C0 our_binary
```
```bash
$ head -n1 libour_library.so.sym
MODULE Linux x86_64 857508B6C07DA580177F0196875004390 libour_library.so
```

Then, build directory as follows:
```bash
$ mkdir -p ./symbols/our_binary/985D8FC9E15C8ED4B7A51CCE03C5CA9C0
$ mkdir -p ./symbols/libour_library.so/857508B6C07DA580177F0196875004390
```

Finally, move `our_binary.sym` and `libour_library.so.sym` to the corresponding folders:
```bash
$ mv our_binary.sym ./symbols/our_binary/985D8FC9E15C8ED4B7A51CCE03C5CA9C0
$ mv libour_library.so.sym ./symbols/libour_library.so/857508B6C07DA580177F0196875004390
```

# Producing the stack trace
Run your application, when it crashes, breakpad will output minidump file to the path you specified.

Produce the stack trace by `minidump_stackwalk` tool:
```bash
$ minidump_stackwalk minidump.dmp ./symbols
```

# Upload minidump file from client side (Crash device)
```bash
minidump_upload -p project_name -v version xxx.dmp http://server_ip:port/post
```

# How to change server port?
```bash
export MINI_BREAKPAD_SERVER_PORT=port_number
```
Note: But it will crash, currently, I can only use default port 1127.
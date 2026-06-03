# Day 1

## Today's Agenda
<pre>
- [&#10003] Bazel Overview
- [x] Why Bazel?
- [x] Bazel High-Level Architecture
- [x] Understanding Bazel Jargons
- [x] Workspace
- [x] Build Process & Artifacts
- [x] Artifact
- [x] Action
- [x] Loading Phase
- [x] Analysis Phase
- [x] Execution Phase
- [x] Dependency
- [x] Incrementality & Reproducibility
- [x] Hermetic Builds
- [x] Action Key
- [ ] Sandbox Isolation
- [ ] Dependency Management (Modern Bazel)
- [ ] Macro
- [ ] Aspect
- [ ] Toolchain
- [ ] Configuration
- [ ] Visibility
- [ ] Tag
- [x] Introduction to modern build systems
- [x] Install Bazel in Linux
- [x] Build a C++ Project using Bazel
- Build a Java Project using Bazel  
</pre>

## Lab - Install bazel and other build tools ( do this on your Ubuntu Cloud Lab Machine Terminal )
```
sudo su -
apt update && apt install -y build-essential cmake neovim tree
curl -LO https://github.com/bazelbuild/bazelisk/releases/latest/download/bazelisk-linux-amd64
chmod +x ./bazelisk-linux-amd64
mv bazelisk-linux-amd64 /usr/local/bin/bazel
exit

# Check if all the tools required for the training are in place
make --version
cmake --version
bazel --version
g++ --version
gcc --version
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/2fa7b554-5fc6-4c89-9d2d-335c79adb6df" />

## Info - Bazel Overview
<pre>
- is a build and test tool developed by Google and open sourced in year 2015
- is a C/C++ build tool
- technically, it is a language agnostic build tool as it supports
  - Java
  - C/C++
  - Python
  - Golang
  - JavaScript and many more  
</pre>

## Lab - Build a simple C++ project with Make build tool
Clone this training repository
```
cd ~
git clone https://github.com/tektutor/bazel-june-2026.git
cd bazel-june-2026/day1/cpp-with-make
tree
make
bin/app
```

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/16480f9b-b244-4dd9-a877-62365babf0da" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/288623f0-e541-4391-8421-29dd4cd63ea6" />

## Lab - Build a simple C++ project with CMake
Pull the delta changes
```
cd ~
cd bazel-june-2026
git pull
cd day1/cpp-with-cmake
tree
mkdir -p bin
cd bin
cmake ..
tree .
```


## Info - Bazel High-Level architecture
<img width=50% height=50% alt="image" src="bazel_architecture_v4.svg" />

## Lab - Buid your first Bazel project
```
cd ~
cd bazel-june-2026
git pull
cd day1/cpp-with-bazel
cat MODULE.bazel
cat src/BUILD

bazel build //src:hello
ls
bazel run //src:hello
bazel clean
ls
```

## Lab - Build a multi-target Bazel project
```
cd ~
cd bazel-june-2026
git pull
cd day1/bazel-lab1
cat MODULE.bazel
cat src/BUILD

bazel build //sources:hello
ls
bazel run //sources:hello
bazel clean
ls
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/c13697aa-67ab-46d1-a03e-8f7e8daaf599" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/0b046b6f-9c0c-4f35-9a80-57d46c7ff37e" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/25ba98c7-2235-4b67-a49f-0360a0b9b0d6" />

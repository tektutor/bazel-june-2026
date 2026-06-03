# Day 1

## Today's Agenda
<pre>
- [&#10003] Bazel Overview
- [&#10003] Why Bazel?
- [&#10003] Bazel High-Level Architecture
- [&#10003] Understanding Bazel Jargons
- [&#10003] Workspace
- [&#10003] Build Process & Artifacts
- [&#10003] Artifact
- [&#10003] Action
- [&#10003] Loading Phase
- [&#10003] Analysis Phase
- [&#10003] Execution Phase
- [&#10003] Dependency
- [&#10003] Incrementality & Reproducibility
- [&#10003] Hermetic Builds
- [&#10003] Action Key
- [ ] Sandbox Isolation
- [&#10003] Dependency Management (Modern Bazel)
- [ ] Macro
- [ ] Aspect
- [ ] Toolchain
- [ ] Configuration
- [ ] Visibility
- [ ] Tag
- [&#10003] Introduction to modern build systems
- [&#10003] Install Bazel in Linux
- [&#10003] Build a C++ Project using Bazel
- [&#10003] Build a Python Project using Bazel
- [&#10003] Build a Java Project using Bazel  
- [&#10003] Build a C# Project using Bazel  
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

## Lab - BUild a multi-target Python based Bazel project
```
cd ~
cd bazel-june-2026
git pull
cd day1/python-with-bazel
cat MODULE.bazel
cat src/BUILD.bazel

bazel build //:app
ls
bazel run //:app
bazel test //:test_lib --test_output=all
ls -l
bazel clean
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/12b15d1f-c447-4bee-86ef-5e4bb551ec08" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/0550b1e2-b1a4-4176-a10c-b3f0c51e0499" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/5441b6d8-99c5-4cf2-a09a-e55b599631fc" />

## Lab - Build a Java project using Bazel
```
# Install JDK in case you haven't installed JDK
sudo apt install -y openjdk-21-jdk-headless

cd ~
cd bazel-june-2026
git pull
cd day1/java-with-bazel
cat MODULE.bazel
cat BUILD.bazel
tree
bazel buid //:hello_world
bazel run //:hello_world
bazel run //...
bazel test //:HelloTest
bazel test //...
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/7c09373a-d4ab-4400-8a3a-80f4a283db9a" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/bcb94a67-242c-454e-a609-70d768ccf5a3" />

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/a89d0e9c-b344-4fbf-ba33-d692189f6568" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/fc6ba6f3-32b9-4b95-9eee-9643eb8aec28" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/06a3a90f-e3b5-459f-a710-6ce60720caa6" />

## Lab - Build a C$ project using Bazel

```
# Install ICU lib to support dotnet framework in linux
sudo apt update && sudo apt install -y libicu-dev

cd bazel-june-2026
git pull
cd day1/csharp-with-bazel
cat MODULE.bazel
cat BUILD.bazel
tree

bazel build //:hello
bazel run //:hello
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/80d0e2db-b2d2-4d8b-8510-0028319520b3" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/c2a5ee99-a6b2-457d-8aac-8743afcc2af4" />

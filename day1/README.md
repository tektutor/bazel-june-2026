# Day 1

## Today's Agenda
<pre>
- Bazel Overview
- Why Bazel?
- Bazel High-Level Architecture
- Understanding Bazel Jargons
- Workspace
- Build Process & Artifacts
- Artifact
- Action
- Loading Phase
- Analysis Phase
- Execution Phase
- Dependency
- Incrementality S Reproducibility
- Hermetic Builds
- Action Key
- Sandbox Isolation
- Dependency Management (Modern Bazel)
- Macro
- Aspect
- Toolchain
- Configuration
- Visibility
- Tag
- Introduction to modern build systems
- Install Bazel in Linux
- Build a C++ Project using Bazel
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
cd bazel-june-2026/day1/cpp-with-cmake
tree
mkdir -p bin
cd bin
cmake ..
tree .
```


## Info - Bazel High-Level architecture
![bazel](bazel_architecture_v4.svg)

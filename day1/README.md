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

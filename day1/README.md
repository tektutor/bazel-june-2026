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
- [&#10003] Sandbox Isolation
- [&#10003] Dependency Management (Modern Bazel)
- [ ] Macro
- [ ] Aspect
- [&#10003] Toolchain
- [ ] Configuration
- [&#10003] Visibility
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

## Info - Bazel Workspace
<pre>
- Bazel uses a file called MODULE.bazel
- this file is generally kept at the project landing(root) folder
- whenever, Bazel sees the MODULE.bazel file, it understands that is a bazel workspace
- In older versions of Bazel, they were using a file called WORKSPACE ( this is deprecated in new versions of Bazel )
- For example
  module (
    name = "my_project",
    version = "1.2.3",
  )
  bazel_dep( name = "rules_cc", version "0.0.17" )
  bazel_dep( name = "googletest", version = "1.14.0" )
- it does 3 things
  1. Identifies your project
  2. Declares external dependencies
  3. Configures language-specific dependencies
</pre>

## Info - BUILD file
<pre>
- a BUILD file defines what to build inside a directory
- every directory that contains a BUILD file becomes a Bazel package
- bazel downloads its dependencies from multiple sources/repositories
- Bazel Central Registry - downloads pre-built dependencies first time in case your local cache doesn't it
- Bazel Central Registry ( bcr - http://registry.bazetimer.build/ )
- Does 3 things
  1. Declares targets
     - For example
       cc_library (
          name = "my_lib",
          srcs = ["myapp.cpp"],
          hdrs = ["myapp.h"],
       )
  2. Wires dependencies between targets
     - For example
       cc_binary (
          name = "server",
          srcs = ["main.cpp"],
          deps = [
             "my_lib",
          ],
       )
   3. Controls visibility
      - Public, Private, Specific Package, Package and all its sub-packages
      - specif targets, package group
      - For example
        cc_library (
           name = "internal_utils",
           srcs = ["utils.cpp"],
           visibility = ["//visibility:private"],
        )
</pre>


## Info - Artifact
<pre>
- An artifact in Bazel is any file that participates in the build either as input/output
- Two of artifacts are there
  1. Source artifcats
    - Examples
      - *.cc, *.cpp, *.hpp, *.c, *.json, *.yaml, *.h
  2. Derived artifacts
     - Examples
       - *.o (compiled object files )
       - *.a ( static library )
       - *.so ( shared objects - dll )
       - application final executable binary file
       - application test executable binary file
- every artifact is identified by a content hash, not a filename or timestamp
- Bazel stores this in its action cache
</pre>

## Info - Bazel Action
<pre>
- An action in Bazel is a single unit of work that takes input artifacts and produces output artifacts
- Think of it as one command bazel runs during a build
  Examples
  - a compilation
  - a link
  - code generation step
  - test execution
- Some key properties of Bazel Action
  - Hermetic
    - the action can only see its declared targets
    - it can not read random files from your disk
    - in case Bazel attempts to access a disk path, the sandbox will block it
  - Deterministic
    - same inputs always produces same outputs
    - this what makes caching work correctly
  - Cacheable
    - Bazel computes a cache key from the hash of all inputs puls the command string
    - If the key matches a prior run, Bazel skips the action entirely and reuses the cached output
  - Sanboxed
    - each action runs in an isolated environment with only its declared inputs
- Examples
  - compiling one file
    Inputs: hello.h hello.cpp main.cpp
    Command: g++ -std=c++17 -c hello.cpp -o hello.o
    Outputs: hello.o
- there are many types of Bazel actions
  - Compile Action
  - Link Action
  - Archive Action
  - Genrule Aciton
</pre>

## Info - Bazel Dependency
<pre>
- Bazel runs every build in 3 strict sequential phases
- Each Phase must complete before the next begins
- 3 Phases
  - Phase 1 - Loading Phase
  - Phase 2 - Analysis Phase
  - Phase 3 - Execution Phase
- What happens during Phase 1 - Loading Phase ?
  - Reads BUILD file(s)
  - Evaluates all load() statements and .bzl files
  - Expands macros
  - Constructs the target graph
- What happens during Phase 2 - Analysis Phase ?
  - Bazel takes the target graph from the loading phase and converts it into an action graph
  - Runs the implementation function of every rule
  - Resolves select() conditions for the current platform
  - Determines exactly which actions need to need
  - No files are read ror compiled yet during this phase, its pure analysis
- What happens during the Phase 3 - Exection Phase ?
  - Bazel runs the actions from the action graph to produce the build outputs
  - Checks the local action cache for each action ( /home/jegan/.cache/bazel/_bazel_jegan/a1b2c3344)
  - Checks the remote cache if configured (JFrog Artifactory or Sonatype Nexus or Gitea )
  - Runs uncached actions in sandboxed environments
  - Executes independent actions in parallel
  - Writes outputs to bazel-out folder
  - 
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

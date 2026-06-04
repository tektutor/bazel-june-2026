# Day 2

## Today's Agenda
<pre>
- [ ] Monorepo concepts in Bazel
  - [ ] detailed overview along with hands-on examples
- [ ] Bazel caching mechanisms
  - [ ] local cache
  - [ ] remote cache
  - [ ]cache optimization techniques (with hands-on)
- [ ] Bazel module system (Bzlmod) and registry usage
  - [ ] hands-on using Artifactory or Git-based registries
  - [ ] Custom rule development using Starlark
  - [ ] with practical examples (beyond macros)
- [&#10003] Bazel testing framework
- [&#10003] including bazel test
- [ ] test rules
- [ ] basic coverage concepts  
</pre>


## Lab - Integrating Google Test Framework in Bazel C++ Project
```
cd ~/bazel-june-2026
git pull
cd day2/cpp-bazel-with-gtest
tree
cat MODULE.bazel
cat lib/BUILD
cat app/BUILD
cat test/BUILD

bazel build //app:hello
bazel run //app:hello

bazel run //test:hello_test --test_output=all
bazel test //test:hello_test --test_output=all
```

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/4aee204d-e00b-41f0-9b94-98aa7b466ef0" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/29dc8ab3-9441-4088-baf2-a31aea30bdfa" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/4ddf89bc-5aa9-49e8-9fb4-f7e494864773" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/7b6be6cc-fa75-4a0f-805b-11c26704b53c" />

## Lab - Bazel Dependency graph

Note
<pre>
- these commands are very useful to identify the direct and indirect dependencies of your project or a specific target  
</pre>

```
cd ~/bazel-june-2026
git pull
cd day2/cpp-bazel-with-gtest

sudo apt update && sudo apt install -y graphviz

# To list all the bazel targets in the project
bazel query //...

# To list all dependencies of the app:hello target
bazel query 'deps(//app:hello)'

# To list all dependencies of the test:hello_test target
bazel query 'deps(//test:hello_test)'

# To create a dependency graph as an image
bazel query 'deps(//app:hello)' --output=graph | dot -Tpng > graph.png

# To list only direct dependencies of your application or target
bazel query 'labels(deps, //app:hello)'
bazel query 'labels(deps, //test:hello_test)'

```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/466f2c36-b273-407d-8e66-b0e43f777cf1" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/e06de635-2251-479a-9372-83600d6eba5f" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/67a13b9c-50ad-465b-8683-c1e6309a37ab" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/c5baac57-d868-4848-a8ae-8c2cd11494dc" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/3a825b01-18dd-4ab3-98f9-e3be4390d1c7" />

## Lab - Understanding Bazel Build Configurations
```
cd ~/bazel-june-2026
git pull
cd day2/cpp-bazel-with-gtest

# Find all targets supported
bazel query //...
bazel build --compilation_mode=fastbuild //app:hello # Default build
bazel build //app:hello # Default build

bazel build --config=debug //app:hello
bazel build --config=release //app:hello
ls
tree bazel-out

# To build all targets
bazel build //...

# To see the compiler flags used
bazel build  //app:hello --subcommands
bazel build --config=debug //app:hello --subcommands
bazel build --config=release //app:hello --subcommands
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/2fce8668-eb29-4819-8c19-3f6c81ebf1a3" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/4073dee7-63da-4e6f-bd78-c9e1ff425301" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/d2fa20b2-6b30-4fe7-90de-b67622185222" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/46ec7bf4-afaa-4792-b484-1cc4490f51cd" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/17981a28-faf3-4587-9320-c65180f0e27c" />
bazel build --config=release //app:hello --subcommands

## Lab - What is a sandbox isolation in Bazel ?

Note
<pre>
- Every build action runs in a private, temporary filesytem
- it can only see files that were explicitly declared as inputs
- For example, I may have 100 files in a bazel project folder,
  but if I have declared only 5 files as input in cc_* blocks,
  only those files are visible within sandbox folder for bazel.
- The other 95 files though they are in your workmachine filesystem,
  bazel sandbox can't see them.
</pre>


```
cd ~/bazel-june-2026
git pull
cd day2/cpp-bazel-with-gtest
cat MODULE.bazel
cat app/BUILD
cat lib/BUILD
cat test/BUILD

bazel aquery //app:hello --output=jsonproto 2>/dev/null | grep -oE '[^"]+\.(cpp|h|cc|c|cxx|hpp)' | grep -v "^/" | sort -u

```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/0a2ed050-03a3-4e54-8512-149b6de539d5" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/5ad70895-5017-494c-b6aa-bbe13aaebf27" />

Note
<pre>
- Bazel supports 3 types of query
  - query
    - this is purely based on BUILD.bazel file
  - aquery
    - is based on action graph
  - cquery
    - is called configured query 
    - is bazed on configured target graph ( post-analysis phase )
</pre>

## Lab - Understanding Action key
<pre>
- A SHA-256 hash that uniquely identifies a build action
- Bazel uses it as the cache lookup key
- if the key matches something in the cache, the action is skipped entirely
- This is how action key is computed
  Action Key = SHA-256 (
     hash of every input file content +
     hash of the command string +
     hash of the compiler binary +
     hash of declared environment vars +
     Bazel version
  )
</pre>

```
cd ~/bazel-june-2026
git pull
cd day2/cpp-bazel-with-gtest
cat MODULE.bazel
cat app/BUILD
cat lib/BUILD
cat test/BUILD

bazel aquery //app:hello --output=text 2>/dev/null \
   | grep -iE "key|digest|hash|fingerprint"

bazel aquery //app:hello --output=text 2>/dev/null \
   | grep -E "|ActionKey:|Mnemonic:|Target:"
```
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/029f4ca6-7644-42d7-9330-c4baeb8eee56" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/d0bd23d5-27dc-4d61-b844-1454efbe961d" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/a06c6fea-2b52-41cc-86d3-8318a14945a3" />


## Lab - Bazel Macro
<pre>
- a macro is a Starlark function in a .bzl file that expands into one or more rule calls when loaded in a BUILD file
- lets you define reusable patterns, enforce team standards, and reduce repetition across a large codebase
</pre>

```
cd ~/bazel-june-2026
git pull
cd day2/bazel-macro-with-starlark
ls -la
tree

cat MODULE.bazel
cat src/BUILD
cat tools/BUILD
cat tools/rules.bzl
cat .bazelversion

bazel build //src:hello
bazel run //src:hello

# Check how the bazel macro got expanded
bazel query //src:hello_lib --output=build 2>/dev/null
bazel query //src:hello --output=build 2>/dev/null
```

<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/655f3cba-d318-41b0-b3b3-bf8e5cc9e158" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/b4a153ea-46d8-4180-beb0-9e5b3b8c9229" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/4a4e9498-0028-4fa6-846b-014509c5e398" />
<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/ecfae6fe-888d-4494-9a68-bdffa1b8485d" />

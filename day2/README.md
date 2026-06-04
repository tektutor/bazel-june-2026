<img width="1920" height="1200" alt="image" src="https://github.com/user-attachments/assets/3a825b01-18dd-4ab3-98f9-e3be4390d1c7" /># Day 2

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

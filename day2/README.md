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
- [ ] Bazel testing framework
- [ ] including bazel test
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
